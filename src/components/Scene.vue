<template>
  <div>
    <!-- We must add the tap-place component to the scene so it has an effect -->
    <a-scene
      xrweb
      tap-place
      xrextras-almost-there
      xrextras-loading
      xrextras-runtime-error
    >
      <!-- We can define assets here to be loaded when A-Frame initializes -->
      <a-assets>
        <!-- Credit to Poly by Google for the model: https://poly.google.com/view/6pwiq7hSrHr -->
        <a-asset-item id="treeModel" src="tree.glb"></a-asset-item>
      </a-assets>

      <!-- The raycaster will emit mouse events on scene objects specified with the cantap class -->
      <a-camera
        position="0 8 0"
        raycaster="objects: .cantap"
        cursor="
          fuse: false;
          rayOrigin: mouse;"
      >
      </a-camera>

      <a-entity
        light="type: directional;
               intensity: 0.8;"
        position="1 4.3 2.5"
      >
      </a-entity>

      <a-light type="ambient" intensity="1"></a-light>

      <!-- Adding the cantap class allows the ground to be clicked -->
      <a-entity
        id="ground"
        class="cantap"
        geometry="primitive: box"
        material="color: #ffffff; transparent: true; opacity: 0.0"
        scale="1000 2 1000"
        position="0 -1 0"
      >
      </a-entity>
    </a-scene>

    <button @click="handleClick">Click Me!</button>
  </div>
</template>

<script>
export default {
  mounted() {
    // Component that places trees where the ground is clicked
    window.AFRAME.registerComponent('tap-place', {
      init: function() {
        const ground = document.getElementById('ground')
        ground.addEventListener('click', event => {
          // Create new entity for the new object
          const newElement = document.createElement('a-entity')

          // The raycaster gives a location of the touch in the scene
          const touchPoint = event.detail.intersection.point
          newElement.setAttribute('position', touchPoint)

          const randomYRotation = Math.random() * 360
          newElement.setAttribute('rotation', '0 ' + randomYRotation + ' 0')

          newElement.setAttribute('visible', 'false')
          newElement.setAttribute('scale', '0.0001 0.0001 0.0001')

          newElement.setAttribute('gltf-model', '#treeModel')
          this.el.sceneEl.appendChild(newElement)

          newElement.addEventListener('model-loaded', () => {
            // Once the model is loaded, we are ready to show it popping in using an animation
            newElement.setAttribute('visible', 'true')
            newElement.setAttribute('animation', {
              property: 'scale',
              to: '0.01 0.01 0.01',
              easing: 'easeOutElastic',
              dur: 800
            })
          })
        })
      }
    })
  },

  methods: {
    handleClick() {
      alert('You clicked me!')
    }
  }
}
</script>

<style scoped>
button {
  position: fixed;
  left: 30px;
  bottom: 30px;
  z-index: 9999999;
}
</style>
