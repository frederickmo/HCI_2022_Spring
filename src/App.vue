<template>
  <div id="container"></div>
</template>

<script>
import * as Three from 'three'
import * as dat from 'dat.gui'
import { TrackballControls } from 'three/examples/jsm/controls/TrackballControls'

// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld
  },

  data () {
    return {
      sizes: {
        width: innerWidth,
        height: innerHeight
      },
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      meshList: []
    }
  },

  methods: {
    init: function() {
      const gui = new dat.GUI()
      const world = {
        box: {
          width: 3
        }
      }
      gui.add(world.box, 'width', 0, 10)
          .onChange(() => {
            this.mesh.geometry.dispose()
            this.mesh.geometry = new Three.BoxGeometry(world.box.width, 3, 3)
          }
      )

      let container = document.getElementById('container');
      // Scene
      this.scene = new Three.Scene();
      // Camera
      this.camera = new Three.PerspectiveCamera(75, this.sizes.width / this.sizes.height, 0.1, 100)
      this.camera.position.x = 0
      this.camera.position.y = -8
      this.camera.position.z = 6
      this.scene.add(this.camera)
      // Renderer
      this.renderer = new Three.WebGLRenderer({
        alpha: true
      });
      this.renderer.setSize(innerWidth, innerHeight);
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
      container.appendChild(this.renderer.domElement);
    },
    model: function() {

      const boxGeometry = new Three.BoxGeometry(3, 3, 3)
      const material = new Three.MeshPhongMaterial({
        color: '#fff'
      })
      this.mesh = new Three.Mesh(boxGeometry, material)
      this.meshList.push({
        id: 0,
        item: this.mesh
      })
      this.mesh.position = new Three.Vector3(0, 0, 1.5)
      this.scene.add(this.mesh)


      this.camera.lookAt(this.mesh.position)


      const planeGeometry = new Three.PlaneGeometry(5, 5)
      const planeMaterial = new Three.MeshPhongMaterial({
        color: '#004455',
        side: Three.DoubleSide
      })
      this.meshList.push({
        id: 1,
        item: new Three.Mesh(planeGeometry, planeMaterial)
      })
      this.scene.add(this.meshList.at(1).item)

      const light = new Three.DirectionalLight('#ffffff', 2)
      light.position.set(0, -5, 5)
      this.scene.add(light)

    },
    autoScale: function() {
      let container = document.getElementById('container')
      window.addEventListener('resize', () =>
      {
        this.sizes.width = container.offsetWidth
        this.sizes.height = container.offsetHeight

        this.camera.aspect = this.sizes.width / this.sizes.height
        this.camera.updateProjectionMatrix()

        this.renderer.setSize(this.sizes.width, this.sizes.height)
        this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
      })

    },
    meshRotate: function() {
      const clock = new Three.Clock()
      const tick = () =>
      {
        const elapsedTime = clock.getElapsedTime()
        this.mesh.rotation.y = 10 * elapsedTime
        this.renderer.render(this.scene, this.camera)
        window.requestAnimationFrame(tick)
      }
      tick()
    },
    traceBallControl: function() {
      const traceBallControls = new TrackballControls(this.camera, this.renderer.domElement)
      const clock = new Three.Clock()
      const that = this
      function renderScene() {

        traceBallControls.update(clock)

        requestAnimationFrame(renderScene)
        that.renderer.render(that.scene, that.camera)
      }
      renderScene()
    },
    animate: function() {
      requestAnimationFrame(this.animate);
      // this.mesh.rotation.x += 0.01;
      // this.mesh.rotation.z += 0.01;
      for (let i = 0; i < this.meshList.length; ++i) {
        this.meshList[i].item.rotation.z += 0.01;
      }
      this.renderer.render(this.scene, this.camera);
    },
  },

  mounted () {
    this.init()
    this.model()
    this.autoScale()
    // this.meshRotate()
    this.traceBallControl()
    this.animate()
    console.log(document.getElementById("container").childNodes)
  }

}
</script>

<style>
*
{
  margin: 0;
  padding: 0;
}

html,
body
{
  height: 100vh;
  font-family: 'Poppins', serif;
  background: #000;
}

#container {
  width: 100vw;
  height: 100vh;
}
</style>
