<template>
  <div id="app">
  
    <div class="scene">
      <div id="clouds">
        <div class="cloud">
          <img src="./assets/images/cloud1.png" style="top: -200px; z-index: 2; left: -120vw;">
        </div>
        <div class="cloud">
          <img src="./assets/images/cloud2.png" style="top: -100px; z-index: 3; left: -150vw;">
        </div>
        <div class="cloud">
          <img src="./assets/images/cloud3.png" style="top: -200px;; z-index: 4; left:-170vw ; ">
        </div>
        <div class="cloud">
          <img src="./assets/images/cloud1.png" style="top: -200px; z-index: 5; left: -120vw;">
        </div>
      </div>
      <div id="card">
        <h1 for="">Login</h1>
        <div>
          <label for="">Nome</label>
          <input  placeholder="Digite seu nome" type="text">
        </div>
        <div>
          <label for="">Senha</label>
          <input type="password" placeholder="Digite sua senha" >
        </div>
        <div style="align-items: center; justify-content: center; display: flex;">
          <a href="https://www.linkedin.com/in/ramon-larcher-b50806270/"><input type="button" class="btn-login" value="Entrar" style="font-size: 20px;"></a>
        </div>
      </div>

      <div id="forest" style="display: flex; bottom: 0; position: absolute; z-index: 20;">
        <img src="./assets/images/forest.png" style="width: 150%;">
      </div>
        <div id="bat" style=" position: absolute; bottom: 0; right: 0; display: flex ; align-items: center; justify-content: center;" >
          <div ref="threeContainer" class="three-container" style="z-index: 15; display: flex; justify-content: center; align-items: center;"></div>
        </div>
    </div>

  </div>
</template>

<script setup>
import {  onMounted } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { PointLight } from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';


const stars = () => {
  let count = 500;
  let scene = document.querySelector('.scene');
  let i = 0;
  while (i < count) {
    let star = document.createElement("i");
    let x = Math.floor(Math.random() * window.innerWidth);
    let y = Math.floor(Math.random() * window.innerHeight);
    let size = Math.random() * 2;
    let duration = Math.random() * 10;

    star.style.left = x + 'px';
    star.style.top = y + 'px';
    star.style.width = 1 + size + 'px';
    star.style.height = 1 + size + 'px';

    star.style.animationDuration = 5 + duration + 's';
    star.style.animationDelay = duration + 's';

    scene.appendChild(star);
    i++;
  }
};

onMounted(() => {
  stars();

  const bat = document.querySelector('#bat');

bat.addEventListener('animationend', () => {
    bat.style.display = 'none';
});

  const container = document.querySelector('#bat');

  const renderer = new THREE.WebGLRenderer({alpha:true});
  renderer.setSize(250, 250);
  container.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = null;
  const camera = new THREE.PerspectiveCamera(45, 1, 1, 3000);
  camera.rotation.y = Math.PI;
  camera.position.x = 800;
  camera.position.y = 100;
  camera.position.z = 1000;

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.update();
  // Luzes posicionadas nos cantos
  const light1 = new PointLight(0xc4c4c4, 3000000);
  light1.position.set(-500, 500, 500); // Superior esquerdo
  scene.add(light1);

  const light2 = new PointLight(0xc4c4c4, 3000000);
  light2.position.set(500, 500, 500); // Superior direito
  scene.add(light2);

  const light3 = new PointLight(0xc4c4c4, 3000000);
  light3.position.set(-500, -500, 500); // Inferior esquerdo
  scene.add(light3);

  const light4 = new PointLight(0xc4c4c4, 300000);
  light4.position.set(500, -500, 500); // Inferior direito
  scene.add(light4);

  const gltfLoader = new GLTFLoader();

  gltfLoader.load('/assets/emerald_bat.glb', (gltf) => {
    const model = gltf.scene;
    model.scale.set(10, 10, 10);
    model.position.y = 100;
    model.position.x = -400
    scene.add(model);

    if (gltf.animations && gltf.animations.length > 0) {
      const mixer = new THREE.AnimationMixer(model);
      gltf.animations.forEach((clip) => {
        mixer.clipAction(clip).play();
      });

      const animate = () => {
        requestAnimationFrame(animate);
        controls.update();
        mixer.update(0.01);
        renderer.render(scene, camera);
      };

      animate();
    } else {
      console.log('Nenhuma animação encontrada no modelo.');
    }
  });
});
</script>

<style>

.scene {
  z-index: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: rgb(2,0,36);
  background: linear-gradient(319deg, rgba(2,0,36,1) 54%, rgba(9,9,121,1) 82%, rgba(199,234,241,1) 98%);
}

@keyframes animateStars {
  0% {
    opacity: 0;
    transform: translateY(0);
  }
  10%, 90% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translateY(-100px);
  }
}

.scene i {
  overflow: hidden;
  z-index: 10;
  position: absolute;
  background: white;
  border-radius: 50%;
  animation: animateStars linear infinite;
  -webkit-box-shadow: 0px 0px 14px 0px rgba(255,255,255,1);
  -moz-box-shadow: 0px 0px 14px 0px rgba(255,255,255,1);
  box-shadow: 0px 0px 14px 0px rgba(255,255,255,1);
}

#card{
  z-index: 30 !important;
  width: 500px;
  height: 500px;
  align-items: center;
  justify-content: center;
  padding: 20px;
  display: flex;
  flex-direction: column;

  /* From https://css.glass */
  background: rgba(255, 255, 255, 0.27);
  border-radius: 16px;
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(3.9px);
  -webkit-backdrop-filter: blur(3.9px);
}
#card>div>input{
  padding-left:4px ;
  margin: 0;
  border: none;
  box-sizing: border-box;
  margin-bottom: 20px;
  border-radius: 20px;
  width: 100%;
  height: 40px;
}
#card>div{
  width: 80%;
}
#card>div>label{
  color: white;
  font-size: 20px;
  font-family: monospace;
}
#card>h1{
  color: white;
  font-size: 25px;
  font-family: monospace;
}
#bat{
  animation: moveBat 15s linear infinite;
}

.cloud>img{
  position: absolute;
  animation: moveClouds 55s linear infinite;
}
#forest>img{
    height: 100%;    
    
  }
#forest{
  margin-left: -50px;    
  width: 120%;
}
.btn-login{
  width: 100%;
  height: 50px;
  border: none;
  padding: 5px;
  background-color: rgb(36 79 178);
  color: white;
  font-family: monospace;
  font-size:30px;
  border-radius:30px;
}
#card>div>a{
  width: 100%;
}
.btn-login:hover{
  background-color:rgb(51 107 236) ;
}
@keyframes moveBat {
  0% {
    transform: translate(0, 0);
  }
  100% {
    transform: translate(-150vw , -150vh);
    
  }
}
@keyframes moveBatMobile {
  0% {
    transform: translate(550px,0);
  }
  100% {
    transform: translate(-300vw , -170vh);    
  }
}

@keyframes moveClouds{
  0%{
    transform: translateX(-220vw);
  }

  100%{
    transform: translateX(300vw);
  }
}

@media only screen and  (max-width:800px){
  #forest{
    width:400%;
  }
  #card{
    width: 100%;
    height: 350px;
  }
  #bat{
    
    animation: moveBatMobile 18s linear infinite;

  }
}


</style>
