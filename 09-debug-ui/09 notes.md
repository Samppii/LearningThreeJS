## Debug UI
An essential part of all creative project is to be able to tweak it easily. The developer and other actors working on the project(like designers or client) must be able to change as many parameters as possible.

In order to take this in account for them to find the perfect color, speed, quantity, etc. for the best experience. You might even find unexpected results that look great.

While you can create your own debug UI using HTML / CSS / JavaScript, there are already multiple libraries:
- [dat.GUI](https://github.com/dataarts/dat.gui)
- [lil-gui](https://lil-gui.georgealways.com/)
- [control-panel](https://github.com/freeman-lab/control-panel)
- [ControlKit](https://github.com/automat/controlkit.js)
- [Uil](https://github.com/lo-th/uil)
- [Tweakpane](https://tweakpane.github.io/docs/)
- [Guify](https://github.com/colejd/guify)
- [Oui](https://github.com/wearekuva/oui)

All of these can do the stuff that we need, but lil-gui is what we're going to be using since it's popular and well maintained.

--- 
## Set UP
To add lil-gui to our project, we can use NPM:

```
npm install lil-gui
```

lil-gui will be available now in the node_modules/ folder and we can import it in our script.js.

```
import `./styles.css`
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import gsap from 'gsap'
import GUI from 'lil-gui'

// ...
```

You can now instantiate lil-gui in a gui variable and we can do that in the very beginning right after imports:

```
// imports...

// Debug
const gui = new GUI()
```

---

## The different types of tweaks

On the top right corner of the experience, you'll see an empty panel. There are different types of tweaks you can add to that panel:
- **Range**: For numbers with minimum and maximum values
- **Color**: For colors and various formats
- **Text**: For simple texts
- **Checkbox**: For booleans (true or false)
- **Select**: For a choice from a list of values
- **Button**: To trigger functions

---
## Range
Most of the tweaks can be added using gui.add( ... ). The first parameter is the object and the second parameter is the property of that object you want to tweak.

You need to add the gui.add(...) after you've created the object and its property. Otherwise, you will ask lil-gui to tweak something that doesn't exist yet.

Adding mesh.position.y:
```
const mesh = new THREE.mesh(geometry, material)
scene.add(mesh)

gui.add(mesh.position,'y')
```

An input should appear in the panel. Changing it will move the cube accordingly. To specify the minimum value, the maximum value, and the precision you can set them in the parameters.

```
const mesh = new THREE.mesh(geometry, material)
scene.add(mesh)

gui.add(mesh.position, 'y', -3, 3, 0.01)
```

By doing that, we get a handy range of input that we can drag and drop.

Can also use the methods min( ... ), max( ... ), and step( ... ) by chaining directly after the add( ... ) method:

```
gui.add(mesh.position, 'y').min(- 3).max(3).step(0.01)
```

And you can also add line breaks:
```
gui
	.add(mesh.position, 'y')
	.min(-3)
	.max(3)
	.step(0.01)
```

To change the label, you can use the name( ... ) method:
```
gui
	.add(mesh.position, 'y')
	.max(3)
	.min(-3)
	.step(0.01)
```

---
## Tweaks for non-properties
One important thing to note is that lil-gui can only modify properties. If you want to update a variable, you can't:

```
let myVariable = 1337
gui.add(myVariable, '???')
```

But you can use some tricks to do so, such as creating an object whose purpose is to hold properties for lil-gui to be used on object:

```
const myObject = {
	myVariable: 2002
}
gui.add(myObject, 'myVariable')
```

---

## Checkbox
lil-gui will automatically what kind of property you want to tweak and use the corresponding interface. A good example is the ``Visible`` property of Object3D. It is a boolean that, if false, will hide the object:

```
gui.add(mesh, 'visible')
```

We can do the same with the wireframe property of materail:

```
gui.add(material, 'wireframe')
```

---

## Colors
Handling colors is a bit harder. We need to use addColor(...) instead of add(...) because the color property is not a string, a boolean, or a number. It's an object with various properties because it's an instance of the THREE.JS Color class. Among those properties are r, g, and b, which lil-gui can use to display a nice tweak:

```
gui.addColor(material, 'color')
```

You'll be able to see a color property on the debug. But when you take the color value from the tweak and apply it to the color property in the code you end up with a wrong color.

This is because Three.js applies some color management in order to optimize the rendering. As a result, the color value that is being displayed in the tweak isn't the same value as the one being used internally.

There are two ways of dealing this.

### Retrieving the modified color
The first solution would be retrieving the color used internally by Three.js thanks to the getHexString() method on the Color instance when the tweak value changes.

To start with, we need to be aware of any tweak change. To do that, we can use the onChange() method:

```
// Pseudocode
gui 
	.addColor(material, 'color')
	.onChange(()=>
	{
		console.log('value has changed')
	})
```

Next, we need to access the Color instance and we can use the classic material.color or we retrieve a value directly as a parameter of the function:

```
// More Pseudocode
gui
	.addColor(material, 'color')
	.onChange(()=>
	{
		console.log('Value has changed')
		console.log('material')
	})
```


Both are the same Color instance from the color property of the material.
So we can use the value and log the result of the getHexString():
```
gui 
	.addColor(material, 'color')
	.onChange(()=>
	{
		console.log(value.getHexString())
	})
```

So, this is the color value that you can safely use in the code.

The problem with this technique is that you need to have the Console open, and this is not handy, especially for designers, or the client.

### Only dealing with non-modified color
The second solution consists of dealing with the color before it gets modified by Three.js. First, we need to save the color somewhere outside of Three.js for instance, creating an object whose purpose is to hold properties.

```
const gui = new Gui()
const debugObject = {}
```

Then, adding a color property to debugObject before creating the cube, we can immediately send it to the color property of the MeshBasicMaterial:

```
debugObject.color = '#3a6ea6'

const geometry = new THREE.BoxGeometry(1,1,1,2,2,2)
const material = new THREE.MeshBasicMaterial({color: debugObject.color, wireframe: false})
```

By doing that, we have the initial color at only one place. Then, instead of the previous tweak we had on the material.color, we are going to change it to debugObject.color:

```
gui
	.addColor(material, 'color')
	.onChange(()=>{
		console.log(value.getHexString())	
	})
```

And finally, instead of doing a console.log(), we can update the material.color using the set() method:

```
gui
	.addColor(material, 'color')
	.onChange(()=>{
		material.color.set(debugObject.color)
	})
```
