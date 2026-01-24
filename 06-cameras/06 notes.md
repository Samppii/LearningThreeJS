### Camera
The #Camera class is an abstract class. You're not supposed to use it directly, but you can inherit from it to have access to common properties and methods. Some of the following classes inherit from the #Camera class.

#### ArrayCamera
The #ArrayCamera is used to render your scene multiple times by using multiple cameras. Each camera will render a specific area of the canvas. You can imagine this looking like an old school console multiplayer games where we had to share split-screen.
#### StereoCamera
The #StereoCamera is used to render the scene through two cameras that mimic the eyes in order to create what we call a parallax effect that will lure your brain into thinking that there is depth. You must have the adequate equipment like a VR headset or red and blue glasses to see the result.
#### CubeCamera
The #CubeCamera is used to get a render facing each direction (forward, backward, leftward, rightward, upward, and downward) to create a render of the surrounding. You can use it to create an environment map for reflection or a shadow map. We'll talk about those later.
#### OrthographicCamera
The #OrthographicCamera is used to create orthographic renders of your scene without perspective. It's useful if you make an RTS game like Age of Empire. Elements will have the same size on the screen regardless of their distance from the camera.

#### PerspectiveCamera
The #PerspectiveCamera is the one that simulates a real-life camera with perspective. 

We'll mostly use #OrthographicCamera and #PerspectiveCamera .

### The PerspectiveCamera
The #PerspectiveCamera class needs some parameters to be instantiated. For example, you can add third and fourth parameters.

```
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 1, 100)
```

#### Field of View
The first parameter is called field of view that corresponds to the camera view's vertical amplitude angle in degrees. If you can use a small angle, you'll end up with a long scope effect, and if you use a wide-angle, you'll end up with a fish eye effect because in the end, what the camera sees will be stretched or squeezed to fit the canvas.

Usually choose 45-75 field of view.

#### Aspect Ratio
The second parameter is called aspect ratio and corresponds to the width divided by the height. While you might think that it's obviously the canvas width by the canvas height and Three.js should calculate it by itself, it's not always the case if you start using Three.js in very specific ways. But in our case, we can use the width and the canvas height.

Save this values so that we can use it multiple times.
```
const sizes = {
	width: 800,
	height: 600
}
```


#### Near and Far
The third and fourth parameters are called near and far, correspond to how close and how far the camera can see. *Any object or part of the object closer to the camera than the #near value or further away from the camera than the #far value will not show up on the render.*

You can see that like in those old racing games where you could see the trees pop up in the distance.

While you might be tempted to use very small and very large values like 0.0001 and 999999 you might end up with a bug called z-fighting where two faces see to fight for which one will be rendered above the other.
[https://twitter.com/Snapman_I_Am/status/800567120765616128](https://twitter.com/Snapman_I_Am/status/800567120765616128)

Always try to use reasonable values like 0.1 and 100

#### OrthographicCamera
The #OrthographicCamera differs from the #PerspectiveCamera by its lack of perspective, meaning that the objects will have the same size regardless of their distance from the camera.

The parameters you have to provide are very different from the #PerspectiveCamera .

Instead of a field of view, you must provide how far the camera can see in direction (left, right, top, and bottom). Then you can provide the near and far values just like the #PerspectiveCamera .

```
const camera = new THREE.OrthographicCamera(-1 , 1, 1, -1, 0.1, 100) // left, right, top, bottom, near, far
```

The rendered will have no perspective, and the sides of the cube will seem parallel. The problem is that the cube doesn't really look cubic.

That is due to the values we provided for the left, right, top, and bottom which are 1 or -1, meaning that we rendered a square area, but that square area stretched to fit the rectangle canvas and our canvas right now isn't a square.

In order to fix that, we can use the canvas aspect ratio (width by height) by creating a variable for instance, aspectRatio (just like the PerspectiveCamera) and store it into it.

```
const aspectRatio = sizes.width / sizes.height
const camera = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100)
```

This will result in a render area width larger than the render area height because our canvas width is larger that its height.

#### Custom Controls

Going back to the #PerspectiveCamera lets move the camera so it faces the cube, and remove the mesh rotation in the mesh function.

```
// Camera
const camera = new THREE.PerspectiveCamera(75, sizes.width/ sizes.height, 1, 1000)

// const aspectRatio = sizes.width / sizes.height
// const camear = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100)

// camera.position.x = 2
// camera.position.y = 2
camera.position.z = 3
camera.lookAt(mesh.position)
scene.add(camera)
```

To add camera control with our mouse. First of all, we want to know the mouse coordinates. We can do that using native JavaScript by listening to the mousemove event with addEventListener.

```
// Cursor
window.addEventListener('mousemove', (event) =>{
	console.log(event.clientX, event.clientY)
})
```

While we could use those values, adjusting is much preferred. By adjusting, it means to have 1 amplitude and that the value can be both negative and positive.

If we only focus on the x value, that would mean that:
- If the cursor is on the far left of the canvas, you should get -0.5
- If the cursor is at the center of the canvas, you should get 0
- If the cursor is on the far right of the canvas, you should get 0.5

While this is not mandatory, it helps to have clean values like that.

Just like sizes variable, you can create a cursor variable with default x and y properties and the update those properties in the mousemove callback:

```
// Cursor
const cursor = {
	x: 0,
	y: 0
}

window.addEventListener('mousemove', (event) =>{
	cursor.x = event.clientX / sizes.width - 0.5
	cursor.y = event.clientY / sizes.height - 0.5
	
	console.log(cursor.x, cursor.y)
})
```


Dividing the event.clientx by sizes.width will give us a value between 0 and 1 (if we keep the cursor above the canvas) while subtracting 0.5 will give a value between -0.5 and 0.5.

We can now have the mouse position stored in the cursor object variable, and can update the position of the camera in the tick function:

```
const tick = () =>{
	// ...
	
	// Updated Camera
	camera.position.x = cursor.x
	camera.position.y = cursor.y
	
	// ...
}
```

The above will work but the axes movements will be wrong. This is due to the position.y axis being positive when going upward in Three.js but the clientY axis being positive when going downward in the webpage.

To fix this, simply invert the cursor.y while updating it by adding a - in front of the whole formula (don't forget the parentheses though):

```
window.addEventListener ('mousemove', (event)=>{
	cursor.x = event.clientX / sizes.width - 0.5
	cursor.y = -(event.clientY / sizes.height - 0.5)
})
```

Finally, you can increase the amplitude by multiplying the cursor.x and cursor.y and ask the camera to look at the mesh using the lookAt(...) method:

```
const tick = () =>{
	// ...
	
	// Updated Camera 2
	camera.position.x = cursor.x * 5
	camera.position.y = cursor.y * 5
	camera.lookAt(mesh.position)
	
}
```

We can go even further by doing a full rotation of the camera around the mesh by using Math.sin(...) and Math.cos(...) 

Sin and Cos, when combined and used with the same angle, enable us to place things on a circle. To do a full rotation, that angle must have an amplitude of 2 times #pi. *Quick notes a full rotation is called a #tau but we don't have an access to this value in JavaScript and we have to use #pi instead*


To increase the radius of that circle, you can simply multiply the result of Math.sin(...) and Math.cos(...):

```
const tick = () =>
{
	// ...
	
	// Updated Camera 3
	camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2
	camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2
	camera.position.y = cursor.y * 3
	camera.lookAt(mesh.position)
	
	// ...
}

tick()
```



