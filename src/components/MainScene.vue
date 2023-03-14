<template>
	<div class="scene">
		<canvas id="canvas-field"></canvas>
		<div class="content">
			<h1 class="content-title">THREE JS</h1>
			<div class="content-description">
				<p class="content-text-1">
					"Sed ut perspiciatis unde omnis iste natus error sit
					voluptatem accusantium doloremque laudantium, totam rem
					aperiam, eaque ipsa quae ab illo inventore veritatis et
					quasi architecto beatae vitae dicta sunt explicabo. Nemo
					enim ipsam voluptatem quia voluptas sit aspernatur aut odit
					aut fugit, sed quia consequuntur magni dolores eos qui
					ratione voluptatem sequi nesciunt. Quis autem vel eum iure
					reprehenderit qui in ea voluptate velit esse quam nihil
					molestiae consequatur, vel illum qui dolorem eum fugiat quo
					voluptas nulla pariatur?"
				</p>
				<p class="content-text-2">
					"But I must explain to you how all this mistaken idea of
					denouncing pleasure and praising pain was born and I will
					give you a complete account of the system, and expound the
					actual teachings of the great explorer of the truth, the
					master-builder of human happiness. No one rejects, dislikes,
					or avoids pleasure itself, because it is pleasure, but
					because those who do not know how to pursue pleasure
					rationally encounter consequences that are extremely
					painful. Nor again is there anyone who loves or pursues or
					desires to obtain pain of itself, because it is pain, but
					because occasionally circumstances occur in which toil and
					pain can procure him some great pleasure. To take a trivial
					example, which of us ever undertakes laborious physical
					exercise, except to obtain some advantage from it? But who
					has any right to find fault with a man who chooses to enjoy
					a pleasure that has no annoying consequences, or one who
					avoids a pain that produces no resultant pleasure?"
				</p>
			</div>
			<div class="content-description">
				<p class="content-text-1">
					"Sed ut perspiciatis unde omnis iste natus error sit
					voluptatem accusantium doloremque laudantium, totam rem
					aperiam, eaque ipsa quae ab illo inventore veritatis et
					quasi architecto beatae vitae dicta sunt explicabo. Nemo
					enim ipsam voluptatem quia voluptas sit aspernatur aut odit
					aut fugit, sed quia consequuntur magni dolores eos qui
					ratione voluptatem sequi nesciunt. Quis autem vel eum iure
					reprehenderit qui in ea voluptate velit esse quam nihil
					molestiae consequatur, vel illum qui dolorem eum fugiat quo
					voluptas nulla pariatur?"
				</p>
				<p class="content-text-2">
					"But I must explain to you how all this mistaken idea of
					denouncing pleasure and praising pain was born and I will
					give you a complete account of the system, and expound the
					actual teachings of the great explorer of the truth, the
					master-builder of human happiness. No one rejects, dislikes,
					or avoids pleasure itself, because it is pleasure, but
					because those who do not know how to pursue pleasure
					rationally encounter consequences that are extremely
					painful. Nor again is there anyone who loves or pursues or
					desires to obtain pain of itself, because it is pain, but
					because occasionally circumstances occur in which toil and
					pain can procure him some great pleasure. To take a trivial
					example, which of us ever undertakes laborious physical
					exercise, except to obtain some advantage from it? But who
					has any right to find fault with a man who chooses to enjoy
					a pleasure that has no annoying consequences, or one who
					avoids a pain that produces no resultant pleasure?"
				</p>
			</div>
		</div>
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

		// controls.constraint.smoothZoom = true;
		// controls.constraint.zoomDampingFactor = 0.2;
		// controls.constraint.smoothZoomSpeed = 5.0;

		camera.position.set(1.75, 1.75, 25);
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

		function animate() {
			requestAnimationFrame(animate);
			cube.rotation.x += 0.005;
			cube.rotation.y += 0.005;
			renderer.render(scene, camera);
		}
		animate();

		document.addEventListener("scroll", () => {
			if (window.scrollY >= 150) {
				gsap.to(camera.position, { z: 15, duration: 2 });
			} else {
				gsap.to(camera.position, { z: 55, duration: 2 });
			}
		});

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

.content {
	color: #ecf0f1;
	padding: 100px 70px;
}

.content-title {
	margin-bottom: 50px;
}

.content-description {
	width: 60vw;
}

.content-description p:not(:last-child) {
	margin-bottom: 20px;
}

.content-description p:last-child {
	margin-top: 50vh;
}
</style>