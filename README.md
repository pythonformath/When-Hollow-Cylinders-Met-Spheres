# When-Hollow-Cylinders-Met-Spheres
Two rotating hollow cylinders and two two spheres where user can control size, spacing, RGB coloring. Language for html, css, javascript.  
I am not quite sure how this started. I need to create a hollow cylinder inside a sphere, so a light beam can go through. I have used Meta AI , it gave me some answers until now. Two cylinders are rotating. PLus two spheres, I need to set up the light beam now. Wish me happy coding!   
The code until now, is: 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rotating Cylinders and Spheres</title>
    <style>
        body {
            margin: 0;
            background-color: #f0f0f0;
            font-family: Monospace;
            font-size: 13px;
            line-height: 24px;
            overscroll-behavior: none;
            text-rendering: optimizeLegibility;
            user-select: none;
            font-smooth: always;
        }
        #c {
            width: 100%;
            height: 100vh;
            display: block;
        }
        .controls {
            position: absolute;
            top: 20px;
            left: 20px;
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            height: 80vh;
            overflow-y: auto;
        }
    </style>
</head>
<body>
    <canvas id="c"></canvas>
    <div class="controls">
        <!-- Existing controls for cylinders -->
        <label>Cylinder 1 Outer Color:</label>
        <input type="color" id="c1-outer-color" value="#ff0000">
        <br>
        <label>Cylinder 1 Inner Color:</label>
        <input type="color" id="c1-inner-color" value="#ffffff">
        <br>
        <label>Cylinder 2 Outer Color:</label>
        <input type="color" id="c2-outer-color" value="#0000ff">
        <br>
        <label>Cylinder 2 Inner Color:</label>
        <input type="color" id="c2-inner-color" value="#ffffff">
        <br>
        <label>Spacing:</label>
        <input type="range" id="spacing" min="1" max="10" value="5" step="0.1">
        <span id="spacing-value">5</span>
        <br>
        <label>Rotation Speed:</label>
        <input type="range" id="speed" min="0.01" max="1" value="0.1" step="0.01">
        <span id="speed-value">0.1</span>
        <br>
        <label>Wall Thickness:</label>
        <input type="range" id="thickness" min="0.1" max="1" value="0.5" step="0.01">
        <span id="thickness-value">0.5</span>
        <br><br>
        <!-- New controls for spheres -->
        <label>Sphere 1 Color:</label>
        <input type="color" id="s1-color" value="#ff00ff">
        <br>
        <label>Sphere 1 Size:</label>
        <input type="range" id="s1-size" min="0.1" max="5" value="1" step="0.1">
        <span id="s1-size-value">1</span>
        <br>
        <label>Sphere 1 Rotation Speed:</label>
        <input type="range" id="s1-speed" min="0.01" max="1" value="0.1" step="0.01">
        <span id="s1-speed-value">0.1</span>
        <br><br>
        <label>Sphere 2 Color:</label>
        <input type="color" id="s2-color" value="#00ffff">
        <br>
        <label>Sphere 2 Size:</label>
        <input type="range" id="s2-size" min="0.1" max="5" value="1" step="0.1">
        <span id="s2-size-value">1</span>
        <br>
        <label>Sphere 2 Rotation Speed:</label>
        <input type="range" id="s2-speed" min="0.01" max="1" value="0.1" step="0.01">
        <span id="s2-speed-value">0.1</span>
        <br>
        <label>Spheres Spacing:</label>
        <input type="range" id="spheres-spacing" min="5" max="20" value="10" step="0.1">
        <span id="spheres-spacing-value">10</span>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="script.js"></script>


<script>

let scene = new THREE.Scene();
let camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);

let renderer = new THREE.WebGLRenderer({
    canvas: document.getElementById('c'),
    antialias: true
});
renderer.setSize(window.innerWidth, window.innerHeight);

let outerRadius = 1;
let height = 5;
let thickness = 0.5;
let innerRadius = outerRadius - thickness;

let outerGeometry = new THREE.CylinderGeometry(outerRadius, outerRadius, height, 32, 1, true);
let innerGeometry = new THREE.CylinderGeometry(innerRadius, innerRadius, height + 0.01, 32, 1, true);

let material1Outer = new THREE.MeshBasicMaterial({ color: 0xff0000, side: THREE.DoubleSide });
let material1Inner = new THREE.MeshBasicMaterial({ color: 0xffffff, side: THREE.DoubleSide });
let material2Outer = new THREE.MeshBasicMaterial({ color: 0x0000ff, side: THREE.DoubleSide });
let material2Inner = new THREE.MeshBasicMaterial({ color: 0xffffff, side: THREE.DoubleSide });

let cylinder1 = createHollowCylinder(outerGeometry, innerGeometry, material1Outer, material1Inner);
let cylinder2 = createHollowCylinder(outerGeometry, innerGeometry, material2Outer, material2Inner);

scene.add(cylinder1);
scene.add(cylinder2);

let sphere1Geometry = new THREE.SphereGeometry(1, 60, 60);
let sphere1Material = new THREE.MeshBasicMaterial({
    map: new THREE.CanvasTexture(createGrainyTexture(256, 256)),
    color: 0xff00ff
});
let sphere1 = new THREE.Mesh(sphere1Geometry, sphere1Material);
scene.add(sphere1);

let sphere2Geometry = new THREE.SphereGeometry(1, 60, 60);
let sphere2Material = new THREE.MeshBasicMaterial({
    map: new THREE.CanvasTexture(createGrainyTexture(256, 256)),
    color: 0x00ffff
});
let sphere2 = new THREE.Mesh(sphere2Geometry, sphere2Material);
scene.add(sphere2);

camera.position.z = 10;

let spacing = 5;
let speed = 0.1;
let sphere1Speed = 0.1;
let sphere2Speed = 0.1;
let spheresSpacing = 10;

function createHollowCylinder(outerGeometry, innerGeometry, outerMaterial, innerMaterial) {
    let outerCylinder = new THREE.Mesh(outerGeometry, outerMaterial);
    let innerCylinder = new THREE.Mesh(innerGeometry, innerMaterial);
    let hollowCylinder = new THREE.Group();
    hollowCylinder.add(outerCylinder);
    hollowCylinder.add(innerCylinder);
    return hollowCylinder;
}

function createGrainyTexture(width, height) {
    let canvas = document.createElement('canvas');
    canvas.width = width;
    canvas.height = height;
    let ctx = canvas.getContext('2d');
    let imageData = ctx.getImageData(0, 0, width, height);
    for (let i = 0; i < imageData.data.length; i += 4) {
        let grain = Math.random() * 128 + 128;
        imageData.data[i] = grain;
        imageData.data[i + 1] = grain;
        imageData.data[i + 2] = grain;
        imageData.data[i + 3] = 255;
    }
    ctx.putImageData(imageData, 0, 0);
    return canvas;
}

function animate() {
    requestAnimationFrame(animate);

    cylinder1.rotation.x += speed;
    cylinder1.rotation.y += speed;
    cylinder2.rotation.x += speed;
    cylinder2.rotation.y += speed;

    cylinder1.position.x = -spacing / 2;
    cylinder2.position.x = spacing / 2;

    sphere1.rotation.x += sphere1Speed;
    sphere1.rotation.y += sphere1Speed;
    sphere2.rotation.x += sphere2Speed;
    sphere2.rotation.y += sphere2Speed;

    sphere1.position.x = -spheresSpacing / 2;
    sphere2.position.x = spheresSpacing / 2;

    renderer.render(scene, camera);
}

animate();

// Event listeners for cylinder controls
document.getElementById('c1-outer-color').addEventListener('input', (e) => {
    cylinder1.children[0].material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('c1-inner-color').addEventListener('input', (e) => {
    cylinder1.children[1].material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('c2-outer-color').addEventListener('input', (e) => {
    cylinder2.children[0].material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('c2-inner-color').addEventListener('input', (e) => {
    cylinder2.children[1].material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('spacing').addEventListener('input', (e) => {
    spacing = parseFloat(e.target.value);
    document.getElementById('spacing-value').innerText = spacing;
});

document.getElementById('speed').addEventListener('input', (e) => {
    speed = parseFloat(e.target.value);
    document.getElementById('speed-value').innerText = speed;
});

document.getElementById('thickness').addEventListener('input', (e) => {
    thickness = parseFloat(e.target.value);
    document.getElementById('thickness-value').innerText = thickness;
    innerRadius = outerRadius - thickness;
    scene.remove(cylinder1);
    scene.remove(cylinder2);
    let outerGeometry = new THREE.CylinderGeometry(outerRadius, outerRadius, height, 32, 1, true);
    let innerGeometry = new THREE.CylinderGeometry(innerRadius, innerRadius, height + 0.01, 32, 1, true);
    cylinder1 = createHollowCylinder(outerGeometry, innerGeometry, material1Outer, material1Inner);
    cylinder2 = createHollowCylinder(outerGeometry, innerGeometry, material2Outer, material2Inner);
    scene.add(cylinder1);
    scene.add(cylinder2);
    cylinder1.children[0].material.color.setHex(material1Outer.color.getHex());
    cylinder1.children[1].material.color.setHex(material1Inner.color.getHex());
    cylinder2.children[0].material.color.setHex(material2Outer.color.getHex());
    cylinder2.children[1].material.color.setHex(material2Inner.color.getHex());
    document.getElementById('c1-outer-color').value = '#' + material1Outer.color.getHexString();
    document.getElementById('c1-inner-color').value = '#' + material1Inner.color.getHexString();
    document.getElementById('c2-outer-color').value = '#' + material2Outer.color.getHexString();
    document.getElementById('c2-inner-color').value = '#' + material2Inner.color.getHexString();
});

// Event listeners for sphere controls
document.getElementById('s1-color').addEventListener('input', (e) => {
    sphere1Material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('s1-size').addEventListener('input', (e) => {
    let size = parseFloat(e.target.value);
    document.getElementById('s1-size-value').innerText = size;
    sphere1.scale.set(size, size, size);
});

document.getElementById('s1-speed').addEventListener('input', (e) => {
    sphere1Speed = parseFloat(e.target.value);
    document.getElementById('s1-speed-value').innerText = sphere1Speed;
});

document.getElementById('s2-color').addEventListener('input', (e) => {
    sphere2Material.color.setHex(parseInt(e.target.value.substring(1), 16));
});

document.getElementById('s2-size').addEventListener('input', (e) => {
    let size = parseFloat(e.target.value);
    document.getElementById('s2-size-value').innerText = size;
    sphere2.scale.set(size, size, size);
});

document.getElementById('s2-speed').addEventListener('input', (e) => {
    sphere2Speed = parseFloat(e.target.value);
    document.getElementById('s2-speed-value').innerText = sphere2Speed;
});

document.getElementById('spheres-spacing').addEventListener('input', (e) => {
    spheresSpacing = parseFloat(e.target.value);
    document.getElementById('spheres-spacing-value').innerText = spheresSpacing;
});



</script>






</body>
</html>
