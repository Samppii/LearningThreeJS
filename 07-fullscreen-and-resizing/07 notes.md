## Introduction
Right now the canvas has 800x600. You don't necessarily need your WebGL to fit the whole screen, but if you want an immersive experience, it might be better.

---
## Fit in the viewport
To make the canvas fit perfectly in the viewport, instead of using fixed numbers in the sizes variable, we use window.innerWidth and window.innerHeight:

```
// ...

// Sizes
const sizes = {
	width: window.innerWidth,
	height: window.innerHeight
}
```

The canvas will now have the width and height of the viewport. Unfortunately, there is a white margin and a scroll bar.

The problem is that browsers all have default stylings like more significant titles, underlined links, space between paragraphs, and paddings on the page. There are many ways of fixing that, and it might depend on the rest of your website. *IMPORTANT: If you have other content try not to break any of those while doing it.*

We're going to fix the position of the canvas using CSS for now.
Since our HTML file is already loading the src/style.css file:

```
<link rel = "stylesheet" href= "./style.css">
```

Can write standard CSS and the page will automatically reload.
A good thing to do first would be to remove any type of margin or padding on all elements by using a wildcard * :
```
* 
{
	margin: 0;
	padding: 0;
}
```

Then, we can fix the canvas on the top left using its webgl class to select it:
```
.webgl
{
	position: fixed;
	top: 0;
	left: 0;
}
```

Don't need to specify the width or height on the canvas because Three.js is already taking care of that when you call the renderer.setSize(...) method.

There might be a blue outline on the canvas when drag and dropping. This mostly happens on latest versions of Chrome. To fix that, we can simply add an outline: none; on the .webgl :

```
.webgl
{
	position: fixed;
	top: 0;
	left: 0;
	outline: none;
}
```

If you need/want to remove any type of scrolling even on touch screens, you can add an overflow: hidden on both html and body:

```
html,
body
{
	overflow: hidden;
}
```

After all this, if you resize the window, the canvas won't follow. So need to deal with the resize.

---
## Handle Resize
To resize the canvas, we first need to know when the window is being resized. To do so, you can listen to the resize event on window.

So basically, add the resize listener right after the sizes variable:

```
window.addEventListener('resize', ()=> {
	console.log('window has been resized')
})
```

Now that we trigger a function when the window is being resized, we need to update few things in our code. First, we must update the sizes variable:

```
window.addEventListener('resize', ()=>{
	// Update Sizes
	sizes.width = window.innerWidth
	sizes.height = window.innerHeight
})
```

Secondly, we must update the camera aspect ratio by changing its aspect property:

```
window.addEventListener('resize', ()=>{
	// ...
	
	// Update Camera
	camear.aspect = sizes.width / sizes.height
})
```

When you change camera properties like aspect you also need to update the projection matrix using camera.updateProjectionMatrix(). More on that in the future:

```
window.addEventListener ('resize', ()=>{
	// ...
	
	camera.updateProjectionMatrix()
})
```

Finally, we MUST update the renderer. Updating the renderer will automatically update the canvas width and height:

```
window.addEventListener('resize', ()=>{
	// ...
	
	// Update Renderer
	renderer.setSize(sizes.width, sizes.height)
})
```

All together:

```
window.addEventListener('resize', ()=>{
	// Update Sizes
	sizes.width = window.innerWidth
	sizes.height = window.innerHeight
	
	// Update Camera
	camera.aspect = sizes.width / sizes.height
	camera.updateProjectionMatrix()
	
	// Update Renderer
	renderer.setSize(sizes.width, sizes.height)
})
```

---
## Handle Pixel Ratio
There might be kind of a blurry render artifacts shaped like stars on the edges(called aliasing). It's because you're testing on a screen with a pixel ratio greater than 1.

The pixel ration corresponds to how many physical pixels you have on the screen for one pixel unit on the software part.

---
### History

Few years ago, all screen had a pixel ratio of 1 and everything was just fine. But when you looked closely at your screen, you could see those pixels, and it was a limitation for how precise images and how thin fonts could be.

The company that did the most about that was Apple. Apple saw and opportunity and started constructing screens with a pixel ratio of 2 called retina. Now, a lot of constructors are doing it and you can see screens with even higher pixel ratios.

While this is a good thing for the image quality, a pixel ratio of 2 means 4 times more pixels to render. And a pixel ratio of 3 means 9 times more pixels to render.

Interesting fact: The highest pixel ratios are usually on the weakest devices --mobile ones.

---
### Handling the pixel ratio

To get the pixel ratio you can use window.devicePixelRatio, and to update the pixel ratio of your renderer, you simply need to call the renderer.setPixelRatio(...)

You might be tempted to simply send the device pixel ratio to that method, but you'll end up with performance issues on high pixel ratio devices.

Having a pixel ratio greater than 2 is mostly marketing. Your eyes will see almost no difference between 2 and 3 but it will create performance issues and empty the battery faster. What you can do is limit the pixel ratio to 2. To do that, you can use Math.min():

```
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
```

There are techniques to be notified when the pixel ratio changes, but it only concerns users having multiple screens with different pixel ratios and they usually resize their window when changing from a screen to another. That is why we simply add this method to the resize callback too:

```
window.addEventListener('resize', ()=>{
	// Update Sizes
	sizes.width = window.innerWidth
	sizes.height = window.innerHeight
	
	// Update Camera
	camera.aspect = sizes.width / sizes.height
	camera.updateProjectionMatrix()
	
	// Update Renderer
	renderer.setSize(sizes.width, sizes.height)
	renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
})
```

