<template>
  <div>
      <div id="app" class="app">
        <h1 class="name opacity" >Nyovwe</h1>
        <p class="description opacity">Designer,<br /> Developer & <br /> Engineer
        </p>
         <span class="buttonContainer opacity">
           <button class="button opacity">
           Click Me
           </button>
           </span>
          </div>
          <canvas id="webgl"></canvas>
        </div>
  </template>

<!-- <canvas id="webgl"></canvas> -->
<script>
import {PlaneGeometry, WebGLRenderer, Scene, PerspectiveCamera, DirectionalLight, MeshPhongMaterial, DoubleSide, Mesh, BufferAttribute, BufferGeometry, Points, PointsMaterial, Float32BufferAttribute, Raycaster } from 'three'
import { gsap } from 'gsap'
import {OrbitControls}  from 'three/examples/jsm/controls/OrbitControls'
import { GUI } from 'lil-gui'

export default {
  mounted() {
  const app = document.querySelector(".app");
  const button = document.querySelector("button");
  const buttonWrap = document.querySelector(".buttonContainer");
  const name = document.querySelector(".name");
  const description = document.querySelector(".description");
  // const gui = new GUI()

  gsap.to(name, {
    opacity: 1,
    duration: 1.5,
    ease: "expo",
    translateY: 0,
  });

  gsap.to(description, {
    opacity: 1,
    duration: 1.5,
    ease: "expo",
    delay: 0.3,
    translateY: 0,
  });

  gsap.to(buttonWrap, {
    opacity: 1,
    duration: 1.5,
    ease: "expo",
    delay: 0.6,
    translateY: 0,
  });

  const canvas = document.getElementById("webgl");

  const initialColor = {
    r: 0,
    g: 0.22,
    b: 0.48,
  };

  const sizes = {
    width: innerWidth,
    height: innerHeight,
  };

  const parameters = {
    width: 400,
    height: 400,
    widthSegments: 50,
    heightSegments: 50,
  };

  // Create scene
  const scene = new Scene();

  // Create camera
  const camera = new PerspectiveCamera(
    75,
    sizes.width / sizes.height,
    0.1,
    1000
  ); // perspective not camera
  camera.position.z = 50; // front and back
  // Camera also has near and far options
  scene.add(camera);

  const light = new DirectionalLight(0xffffff, 1); // add directional light, phong material does not display without the light
  light.position.set(0, 1, 1);
  scene.add(light);

  const backLight = new DirectionalLight(0xffffff, 1); // add directional light, phong material does not display without the light
  backLight.position.set(0, 1, -1);
  scene.add(backLight);


  // Create a plane
  const planeGeometry = new PlaneGeometry(
    parameters.width,
    parameters.height,
    parameters.widthSegments,
    parameters.heightSegments
  );

  const planeMaterial = new MeshPhongMaterial({
    // color: 0xff0000,
    side: DoubleSide,
    flatShading: true, // flat shading
    vertexColors: true,
    // wireframe: true,
  });
  const plane = new Mesh(planeGeometry, planeMaterial);

  // console.log(plane.geometry.attributes.position);

  const modifyPlane = () => {
    planeGeometry.dispose();
    planeMaterial.dispose();

    plane.geometry = new PlaneGeometry(
      parameters.width,
      parameters.height,
      parameters.widthSegments,
      parameters.heightSegments
    );

    const { count } = plane.geometry.attributes.position;
    const { array } = plane.geometry.attributes.position;

    // console.log(plane.geometry.attributes.position);

    const random = [];

    // positions
    for (let i = 0; i < array.length; i++) {
      const i3 = i * 3;

      const x = array[i3];
      const y = array[i3 + 1];
      const z = array[i3 + 2];

      array[i3] = x + Math.random() - 0.5 * 3;
      array[i3 + 1] = y + Math.random() - 0.5 * 3;
      array[i3 + 2] = z + Math.random() - 0.5 * 3;

      random.push(Math.random() * Math.PI * 2);
    }

    plane.geometry.attributes.position.originalPositions = array;
    plane.geometry.attributes.position.randomPositions = random;

    // console.log(plane.geometry.attributes.position);

    const colors = [];

    // colors
    for (let i = 0; i < count; i++) {
      colors.push(initialColor.r, initialColor.g, initialColor.b);
    }

    plane.geometry.setAttribute(
      "color",
      new BufferAttribute(new Float32Array(colors), 3)
    );
  };

  modifyPlane();

  scene.add(plane);

  const pointsCount = 5000;
  const pointGeometry = new BufferGeometry();
  const pointsMaterial = new PointsMaterial({
    size: Math.random() * 2,
    sizeAttenuation: true,
    color: 0xffffff,
  });

  const pointPositions = new Float32Array(pointsCount * 3);

  for (let i = 0; i < pointPositions.length; i++) {
    const x = (Math.random() - 0.5) * 2000;
    const y = (Math.random() - 0.5) * 2000;
    const z = (Math.random() - 0.5) * 2000;

    pointPositions[i] = x;
    pointPositions[i + 1] = y;
    pointPositions[i + 2] = z;
  }

  pointGeometry.setAttribute(
    "position",
    new Float32BufferAttribute(pointPositions, 3)
  );

  const points = new Points(pointGeometry, pointsMaterial);

  scene.add(points);

  const raycaster = new Raycaster();
  const mouse = {
    x: 0,
    y: 0,
  };

  // GUI
  // gui.add(parameters, "width").min(100).max(500).step(1).onChange(modifyPlane);
  // gui.add(parameters, "height").min(100).max(500).step(1).onChange(modifyPlane);
  // gui
  //   .add(parameters, "widthSegments")
  //   .min(25)
  //   .max(100)
  //   .step(1)
  //   .onChange(modifyPlane);
  // gui
  //   .add(parameters, "heightSegments")
  //   .min(25)
  //   .max(100)
  //   .step(1)
  //   .onChange(modifyPlane);

  // gui.addColor(hoverColor, "r");
  // gui.addColor(hoverColor, "g");
  // gui.addColor(hoverColor, "b");

  // Create renderer
  const renderer = new WebGLRenderer({ canvas, alpha: true });
  renderer.setSize(sizes.width, sizes.height);
  renderer.setPixelRatio(Math.min(devicePixelRatio, 2)); // devicePixel ratio or two => find the smallest
  renderer.render(scene, camera);

    // Orbit
    const controls = new OrbitControls(camera, canvas);
  controls.enabled = true;
  controls.enableDamping = true;


  let dt = 0;
  function animate() {
    // const time = Date.now();
    // const dt = time - ot;
    // ot = time;

    dt += 0.08;

    // update controls
    // controls.update();

    const { array, originalPositions, randomPositions } =
      plane.geometry.attributes.position;

    for (let i = 0; i < array.length; i += 3) {
      // const i3 = i * 3;

      //  x
      array[i] = originalPositions[i] + Math.cos(dt + randomPositions[i]) * 0.02;

      //  y
      array[i + 1] =
        originalPositions[i + 1] + Math.sin(dt + randomPositions[i + 1]) * 0.02;
    }

    plane.geometry.attributes.position.needsUpdate = true;

    renderer.render(scene, camera);

    raycaster.setFromCamera(mouse, camera); //raycaster setFromCamera

    const intersects = raycaster.intersectObject(plane);

    const hoverColor = {
      r: 0.1,
      g: 0.5,
      b: 1,
    };

    if (intersects.length > 0) {
      const { a, b, c } = intersects[0].face;

      const { color } = intersects[0].object.geometry.attributes;

      // vertice 1
      color.setX(a, hoverColor.r);
      color.setY(a, hoverColor.g);
      color.setZ(a, hoverColor.b);

      // vertice 2
      color.setX(b, hoverColor.r);
      color.setY(b, hoverColor.g);
      color.setZ(b, hoverColor.b);

      // vertice 3
      color.setX(c, hoverColor.r);
      color.setY(c, hoverColor.g);
      color.setZ(c, hoverColor.b);

      color.needsUpdate = true;

      gsap.to(hoverColor, {
        r: initialColor.r,
        g: initialColor.g,
        b: initialColor.b,
        onUpdate: () => {
          // vertice 1
          color.setX(a, hoverColor.r);
          color.setY(a, hoverColor.g);
          color.setZ(a, hoverColor.b);

          // vertice 2
          color.setX(b, hoverColor.r);
          color.setY(b, hoverColor.g);
          color.setZ(b, hoverColor.b);

          // vertice 3
          color.setX(c, hoverColor.r);
          color.setY(c, hoverColor.g);
          color.setZ(c, hoverColor.b);

          color.needsUpdate = true;
        },
      });
    }

    points.rotation.x += 0.001;

    // loop
    requestAnimationFrame(animate);
  }

  animate();

  window.addEventListener("resize", () => {
    sizes.width = innerWidth;
    sizes.height = innerHeight;
    const app = document.getElementById("app");

    app.style.placeSelf = "center";

    camera.aspect = sizes.width / sizes.height;

    camera.updateProjectionMatrix();

    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
  });

  window.addEventListener("mousemove", (e) => {
    mouse.x = (e.clientX / sizes.width) * 2 - 1;
    mouse.y = -(e.clientY / sizes.height) * 2 + 1;
  });

  window.addEventListener("touchstart", (e) => {
    const { clientX, clientY } = e.touches[0];
    mouse.x = (clientX / sizes.width) * 2 - 1;
    mouse.y = -(clientY / sizes.height) * 2 + 1;
  });

  buttonWrap.addEventListener("click", (e) => {
    e.preventDefault();

    gsap.to(app, {
      opacity: 0,
      // onComplete: () => {
      //   app.style.setProperty("display", "none");
      // },
    });

    // console.log(camera.rotation.x);

    gsap.to(camera.position, {
      z: 25,
      ease: "power3.inOut", // enter fase, leave slow
      duration: 2,
    });

    gsap.to(camera.rotation, {
      x: degToRad(90),
      ease: "power3.inOut",
      duration: 2,
    }); // rotation in radians

    gsap.to(camera.position, {
      y: 1000,
      ease: "power3.in",
      duration: 1,
      delay: 2,
      onComplete: () => {
        // window.open("https://www.youtube.com/watch?v=uYuBJtMJIKU", "_blank");
        this.$router.push('/work')
      },
    });
  });

  // console.log(degToRad(180));

  // fading colors with GSAP

  function degToRad(deg) {
    return (deg * Math.PI) / 180;
  }

    }
}
</script>

<style>
body{
  margin: 0;
  padding: 0;
  gap: 0;
  background-color: rgb(0, 0.22, 0.48);
  -webkit-font-smoothing: antialiased;
}


.opacity{
  opacity: 1
}


p, a{
  margin: 0;
  padding: 0;
}

.app{
  position: absolute;
  color: white;
  text-align: center;
  font-family: "Space Mono";
  display: grid;
  gap: 8px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}


.name{
font-size: 16px;
user-select: none;
transform: translateY(30px);
}

.description{
  font-family: 'Exo';
  user-select: none;
  font-style: italic;
  font-size: 32px;
  font-weight: bold;
  transform: translateY(30px);
}

a{
  color: white;
}

.button{
  background: none;
  border: none;
  color: white;
  border: 1px solid white;
  padding: 12px 16px;
  font-size: 14px;
  text-transform: uppercase;
  border-radius: 12px;
  cursor: pointer;
  font-family: 'Space mono';
  transform: translateY(30px);
  opacity: 1;
}

.button:hover{
  /* transform: translateY(-5px); */
  /* transition: all 0.5ms cubic-bezier(0.075, 0.82, 0.165, 1); */
  background-color: white;
  color: rgb(44, 44, 44)
}
</style>