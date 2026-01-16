
#### First of all, how to configure it?
* You can basically use vite to configure it ---> Vite Configuration(Bash) on your bash
```	
	npm create vite@latest . //Creates the project inside the current directory
	npm install // installs all the node modules and packages
	npm run dev // runs it locally on the machine
```

###### Since we're doing three,
* Installing dependencies would be, 
		```npm install three```

###### Now, back to importing Three.js  //Script.js or wherever you're scripting it
```
import * as THREE from 'three'

Console.log(THREE) //Wil see a lot of stuff going on the console
```

###### THREE has a lot of classes in THREE variables but not all classes are inside this variable. So, need to instantiate it, For instance;

* Creating a scene using THREE
```
const scene = new THREE.scene() // Creates a new THREE scene
const sphereGeometry = new THREE.sphereGeometry(1.5, 32, 32) //Creates a new sphere Geometry
```

* First Scene (Need four elements to get started)
	* A scene that will contain objects
	* Some objects
	* A camera
	* A renderer

#### Scene: 
The Scene is like a container. You place your objects, models, particles, lights, etc in it, and at some point, you ask THREE.JS to render that scene.

###### To create a scene, use the Scene class:
```
// Scene
const scene = new THREE.Scene()
```

#### Object:
Objects can be anything; primitive geometries, imported models, particles, lights, and so on.

* Creating a simple cube (red) `Has many complex geometries and matrixes`
* To create a red cube tho, we need to create a type of object named #Mesh. A mesh is a combination of a *Geometry* (The Shape) and a *Material* (How it looks)

###### To create a geometry;
We use BoxGeometry class with the first 3 parameters that corresponds to the Box's Size

```
//Object
const geometry = new THREE.BoxGeometry(1,1,1)
```

###### To create a material;
We use the MeshBasicMaterial class with one parameter -----> an object {} containing all the options. All we need to specify is the color property.

```
const geometry = new THREE.BoxGeometry(1,1,1)
const material = new THREE.MeshBasicMaterial({color : 0xff0000}) //Color red
```

###### Finally,
To create the Final Mesh, we use the Mesh Class and send the Geometry and the materials as *Parameters*

```
const geometry = new THREE.BoxGeometry(1,1,1)
const material = new THREE.MeshBasicMaterial({color : 0xff0000}) //Color red
const mesh = new THREE.Mesh(geometry, material)

//Now we can add the mesh to the scene by using the add(...)
scene.add(mesh)
```

#### Camera:
- Camera is not visible it's more like a theoretical point of view. When rendering a scene it will be from a camera's point of view. We can have multiple cameras and switch between them but we usually only use one.

- There are multiple cameras (For future notes) For example:
	- Perspective Camera ---> A class to create the camera.
- There are two essentials that we need to provide for the camera though.
	- **Field of View**
		- How large your vision angle is. If you use a very large angle, you'll be able to see in every directions at once but with much distortions, because the results will be drawn on a small rectangle.
		- If you use a small angle, things will look zoomed in. The field of view (FOV) is expressed in degrees and corresponds to the vertical vision angle. Using 75 degrees for now on 03.
	- **Aspect Ratio**
		- In most cases, the aspect ratio is the width of the canvas divided by it's height. so,
```
// Sizes[This an object btw]
const sizes = {
	width : 800,
	height : 600
}

// Camera
const camera = new THREE.PerspectiveCamera(sizes.width/sizes.height)
scene.add(camera)
```

#### Renderer:
A renderer's job is to render(duh), We're simply just going to ask the renderer to render our scene from the camera's point of view, and the result will be drawn into a canvas.

You can create the canvas by yourself, or let the renderer generate it, doing it manually right now:
	In the index.html file, instead of giving it a < h1 >, we're creating the < canvas > element before loading the scripts and giving it a class.
```
< canvas class = "webgl" > </ canvas >
```

In order to create a renderer, we use the WebGLRenderer class with one parameter: An Object { } containing all the options. We need to specify the canvas property corresponding to the < canvas > we added to the page.
i.e., Fetch and store the < canvas > element we created on a variable.

```
const canvas = document.querySelector('canvas.webgl')
```

We also need to update the size of renderer with setSize(...) method using the sizes object we created earlier. The setSize(...) method will automatically resize our < canvas > accordingly.

```
// Example
// Canvas
const canvas = document.querySelector('canvas.webgl')

//...

//Renderer
const renderer = new THREE.WebGlRenderer({
	canvas : canvas
})

renderer.setSize(sizes.width, sizes.height)

//IMPORTANT: FIRST RENDER, CALL THE RENDER(...) METHOD AND SEND IN THE SCENE AND CAMERA AS PARAMETERS:

renderer.render(scene, camera)

```

Running this upper code will give you a black screen dw we're still cooking hah I dabble.
But why a black screen though? Where's the object we created?

- **The ISSUE**
	- We haven't specified our object's position, nor our camera's. Both are in default position, which is the center of the scene and we can't see an object from it's inside(BY DEFAULT)
	- So, we need to move things, and we have multiple properties such as position, rotation, and scale. So using the position property to move the camera backward.
##### Position:
The position property is an object with three relevant properties: x, y & z. By default, THREE.js considers the forward / backward axis to be z. To move the camera backward, we need to provide a position value to that property. We can do that anywhere once we've created the camera variable, yet it has to happen before the render.

```
// Camera
const camera = new THREE.PerspectiveCamera(75, sizes.width/sizes.height)
camera.position.z = 3
scene.add(camera)
```

##### THE FINAL CODE:
```
// Canvas
const canvas = document.querySelector('canvas.webgl')

// Scene
const scene = new THREE.Scene()

// Object (Adding a red cube, square in this case)
const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshMaterial({color : 0xff0000})
const mesh = new THREE.Mesh(geometry, material)

scene.add(mesh) //gotta add the mesh to the scene

// Sizes of objects
const sizes = {
	width : 800,
	height : 600
}

// Camera
const camera = new THREE.PerspectiveCamear(75, sizes.width/sizes.height)
camera.position.z = 3 // Takes the camera infront of the object
scene.add(camera) //gotta add the camera to the scene

// Renderer
const renderer = new THREE.WebGLRenderer({
	canvas : canvas
})
renderer.setsize(sizes.width, sizes.height)
renderer.render(scene, camera) // Have to render both or else you're cooked
```
