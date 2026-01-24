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

