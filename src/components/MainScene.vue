<template>
	<div class="scene">
		<canvas id="canvas-field"></canvas>
	</div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

export default {
	name: "scene",
	data() {
		return {
			scrolledUpAfterTrigger: false,
			cameraPosition: {
				x: 2,
				y: 2,
				z: 30,
			},
			cubeVertices: [
				// front
				-1, -1, 1, 1, -1, 1, -1, 1, 1, -1, 1, 1, 1, -1, 1, 1, 1, 1,
				// back
				1, -1, -1, -1, -1, -1, 1, 1, -1, 1, 1, -1, -1, -1, -1, -1, 1,
				-1,
				// left
				-1, -1, -1, -1, -1, 1, -1, 1, -1, -1, 1, -1, -1, -1, 1, -1, 1,
				1,
				// right
				1, -1, 1, 1, -1, -1, 1, 1, 1, 1, 1, 1, 1, -1, -1, 1, 1, -1,
				// top
				1, 1, -1, -1, 1, -1, 1, 1, 1, 1, 1, 1, -1, 1, -1, -1, 1, 1,
				// bottom
				1, -1, 1, -1, -1, 1, 1, -1, -1, 1, -1, -1, -1, -1, 1, -1, -1,
				-1,
			],
			cubeXSpeed: 0.005,
			cubeYSpeed: 0.005,
			sizes: {
				width: window.innerWidth,
				height: window.innerHeight,
			},
		};
	},
	mounted() {
		const scene = new THREE.Scene();
		scene.background = new THREE.Color(0x222529);
		const camera = new THREE.PerspectiveCamera(
			45,
			window.innerWidth / window.innerHeight,
			0.1,
			1000
		);
		scene.add(camera);

		const light = new THREE.DirectionalLight(0xffffff, 2);
		light.position.set(0, 1, 2);
		light.castShadow = true;
		scene.add(light);

		const canvas = document.querySelector("#canvas-field");
		const renderer = new THREE.WebGLRenderer({ canvas });
		renderer.setSize(this.sizes.width, this.sizes.height);
		const controls = new OrbitControls(camera, renderer.domElement);
		controls.enableZoom = false;
		controls.enableRotate = false;
		controls.enablePan = false;

		camera.position.set(
			this.cameraPosition.x,
			this.cameraPosition.y,
			this.cameraPosition.z
		);
		controls.update();

		// GEOMETRY
		const geometry = new THREE.BufferGeometry();
		const vertices = new Float32Array(this.cubeVertices);
		geometry.setAttribute(
			"position",
			new THREE.BufferAttribute(vertices, 3)
		);
		const material = new THREE.MeshStandardMaterial({
			color: 0x30336b,
			flatShading: true,
			// wireframe: true,
		});
		const cube = new THREE.Mesh(geometry, material);
		scene.add(cube);

		// GSAP animation
		gsap.registerPlugin(ScrollTrigger);

		let tl = gsap
			.timeline({
				scrollTrigger: {
					trigger: ".scene",
					start: "top top",
					end: "30%",
					scrub: 2,
					// markers: true,
				},
			})
			.to(camera.position, { z: 10 });

		gsap.timeline({
			scrollTrigger: {
				trigger: ".scene",
				start: "30%",
				end: "60%",
				scrub: 1,
				// markers: true,
			},
		}).to(this, { cubeXSpeed: 0.05, cubeYSpeed: 0.05 });

		gsap.timeline({
			scrollTrigger: {
				trigger: ".scene",
				start: "60%",
				end: "85%",
				scrub: 1,
				// markers: true,
			},
		}).to(this, { cubeXSpeed: 0.005, cubeYSpeed: 0.005 });

		const animate = () => {
			requestAnimationFrame(animate);
			cube.rotation.x += this.cubeXSpeed;
			cube.rotation.y += this.cubeYSpeed;
			renderer.render(scene, camera);
			camera.lookAt(0, 0, 0);
		};
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
				b: color.b,
			});
		});
	},
};
</script>

<style scoped>
#canvas-field {
	z-index: -1;
	position: fixed;
	width: 100vw;
	height: 100vh;
	top: 0;
	left: 0;
}

.scene {
	height: 600vh;
}
</style>