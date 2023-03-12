<template>
	<div class="scene">
		<canvas id="canvas-field"></canvas>
		<button class="transform-button">click</button>
	</div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import gsap from "gsap";

export default {
	name: "scene",
	data() {
		return {
			geometry: null,
			sizes: {
				width: window.innerWidth,
				height: window.innerHeight,
			},
		};
	},
	mounted() {
		const scene = new THREE.Scene();
		scene.background = new THREE.Color(0x222529);
		// fov, width, height, how close and far can I zoom before object disappear
		const camera = new THREE.PerspectiveCamera(
			45,
			window.innerWidth / window.innerHeight,
			0.1,
			1000
		);
		camera.position.set(1.75, 1.75, 25);
		scene.add(camera);

		// color, intensity, distance, decay
		const light = new THREE.PointLight(0xffffff, 5, 100);
		light.position.set(10, 10, 10);
		scene.add(light);

		const canvas = document.querySelector("#canvas-field");
		const renderer = new THREE.WebGLRenderer({ canvas });
		renderer.setSize(this.sizes.width, this.sizes.height);
		const controls = new OrbitControls(camera, renderer.domElement);

		const geometry = new THREE.BoxGeometry(5, 5, 5);
		const material = new THREE.MeshPhysicalMaterial({
			color: 0x30336b,
			roughness: 0.4,
		});
		const cube = new THREE.Mesh(geometry, material);
		scene.add(cube);

		const tranformBtn = document.querySelector(".transform-button");
		tranformBtn.addEventListener("click", () => {
			for (var i = 0, l = geometry.vertices.length; i < l; i++) {
				var vertex = geometry.vertices[i];
				vertex.normalize().multiplyScalar(550);
			}
		});

		function animate() {
			requestAnimationFrame(animate);
			cube.rotation.x += 0.005;
			cube.rotation.y += 0.005;
			renderer.render(scene, camera);
		}
		animate();

		window.addEventListener("resize", () => {
			this.sizes.width = window.innerWidth;
			this.sizes.height = window.innerHeight;
			//update camera
			camera.aspect = this.sizes.width / this.sizes.height;
			camera.updateProjectionMatrix();
			renderer.setSize(this.sizes.width, this.sizes.height);
		});

		window.addEventListener("mousemove", (e) => {
			let xCoord = e.clientX;
			let yCoord = e.clientY;
			let red = ((xCoord / this.sizes.width) * 255).toFixed(0);
			let green = ((yCoord / this.sizes.height) * 255).toFixed(0);
			let blue = 140;
			let color = new THREE.Color(`rgb(${red}, ${green}, ${blue})`);

			gsap.to(cube.material.color, {
				r: color.r,
				g: color.g,
				b: color.blue,
			});
		});
	},
};
</script>

<style scoped>
.transform-button {
	position: absolute;
	bottom: 50px;
	left: 0;
	right: 0;
	width: 100px;
	padding: 10px;
	margin: 0 auto;
	font-size: 1.2em;
	outline: none;
	border: 2px solid transparent;
	transition: all 0.3s ease-in-out;
	cursor: pointer;
	background-color: #000;
	color: #fff;
}

.transform-button:hover {
	background-color: transparent;
	border: 2px solid #9198e2;
}
</style>