### Animations
When using Three.JS, animations work like stop motion. You move the objects, and you do a render. Then you move the objects a little more, and you do another render and so on. the more you move the objects between the render the more, the smoother they'll appear while moving.

**Frame Rate** is the screen that you're looking at a specific frequency. The Frame Rate mostly depends on the screen, but the computer itself has limitations. Most screens run at 60 fps. Doing the math, it means that about a frame every 16ms. But some screens can run much faster, and when the computer has difficulties processing things, it'll run more slowly.

### requestAnimationFrame
The primary use of requestAnimationFrame is not to run code on each frame.

- requestAnimationFrame will execute the function provided on the next frame. But them, if this function also uses requestAnimationFrame to execute that same function on the next frame, you'll end up with your function being executed on each frame forever which is exactly what we want.

Creating a function called tick and call this function once. In this function, use window.requestAnimationFrame(...) to call this same function on the next frame:

```
// Animate
const tick = () =>
{
	console.log('tick')
	window.requestAnimationFrame(tick)
}

tick() // Creates an infinite loop
```

As we can see on the console, the 'tick' is being called on each frame. Testing this code on a computer with a high frame rate, the 'tick' will appear at high frequency.

You can now move the renderer.render(...) call inside that function and increase the cube rotation:

```
// Animate
const tick = () =>
{
	// Update Objects
	mesh.rotation.y += 0.01
	
	// Render
	renderer.render(scene.camera)
	
	// Call tick again on the next frame
	window.requestAnimationFrame(tick)
}

tick() // Infinite loop to keep the animation constantly running
```

The code should give you an animation, but the problem would be if you test this code on a computer with high frame rate, the cube will rotate faster, and if you test on a lower frame rate, the cube will rotate slower.

### Adaptation to the Frame Rate
To adapt the animation to the frame rate, we need to know how much time it's been since the last tick.
First, we need a way to measure time. In native JS, you can use Date.now() to get the current timestamp:

```
const time = Date.now()
```

The timestamp corresponds to how much time has passed since the first of Jan 1970 (the beginning of time for Unix). In JS, its unit is in milliseconds.

What you need now is to subtract the current timestamp to that of the previous frame to get what we can call the deltaTime and use this value when animating objects:

```
// Animate
let time = Date.now() // Stores the time stamp of the last frame

const tick =()=>
{
	// Time
	const currentTime = Date.now() // Gets current timestamp
	const deltaTime = currentTime - time // Calculates time since last frame
	time = currentTime // Updates for next frame
	
	// Update objects and Multipy rotation by deltaTime
	mesh.rotation.y += 0.01 * deltaTime
}

tick()
```

While this code above isn't really that complicated, there's a built-in solution in THREE.JS that actually does all these for you instead Clock.

#### Clock
You simply have to instantiate a Clock variable and use the built-in methods like getElapsedTime(). This method will return how many seconds have passed since the Clock was created.

```
// Animate
const clock = new THREE.Clock()
const tick = () =>
{
	const elapsedTime = clock.getElapsedTime()
	
	// Updated Objects
	mesh.rotation.y = elapsedTime
	
	// ...
}

tick()
```

With getElapsedTime():

```
mesh.rotation.y = elapsedTime // Sets to elapsed time (not +=)
```
- This returns total time since clock started
- You set the rotation directly
- Rotation = time in radians

With  getDelta(), you get the original "return time since last frame" effect

```
const deltaTime = clock.getDelta()
mesh.rotation.y += deltaTime // Adds delta time (+=)
```
- Returns time since last frame
- You add to the rotation each frame

Both adapt to FPS, just different styles:
- **elapsedTime**: "Set rotation to match total time"(Simpler)
- **deltaTime**: "Increment rotation each frame"(more control)

For simple rotation, elapsedTime is cleaner but for more complex animations where you need precise speed control, deltaTime gives you more flexibility.

You can also use it to move things like position property. If you combine it with Math.sin(...) you can get smooth oscillating motion.

```
// Animate

const clock = new THREE.Clock()

const tick =()=>
{
	const elapsedTime = clock.getElapsedTime()
	
	// Update Objects
	mesh.position.x = Math.cos(elapsedTime)
	mesh.position.y = Math.sin(elapsedTime)
	
	//...
}

tick()
```

And you can also use this technique to animate any Object3D like camera:
```
// Animate

const clock = new THREE.Clock()

const tick =()=>
{
	const elapsedTime = clock.getElapsedTime()
	
	// Update Objects
	camera.position.x = Math.cos(elapsedTime)
	camera.position.y = Math.sin(elapsedTime)
	camera.lookAt(meshPosition)
	
	//...
}

tick()
```

Quick NOTE: 

Math.sin() creates a wave pattern that goes up and down smoothly.
![[CleanShot 2026-01-22 at 14.36.51.png]]

Math.cos() is exactly like Math.sin() - it creates the same wave pattern between -1 and 1..BUT it's shifted by 90 degrees.

![[CleanShot 2026-01-22 at 14.38.17.png]]

#### Using a library

Sometimes, you'll want to animate the scene in a very specific way that will require using another library. There are tons of animation libraries, but GSAP can be used.

To add GSAP, just use the dependency manager npm which is,
```
npm install gsap
```

Simply import it in the script.js:
```
import './style.css'
import * as THREE from 'three'
import gsap from 'gsap'
```

Example:
```
// Animate
gsap.to(mesh.position, { duration:1, delay:1, x:2})

const tick =()=>
{
	// Render
	renderer.render(scene, camera)
	
	// Call tick again on the next frame
	window.requestAnimationFrame(tick)
}

tick()
```

