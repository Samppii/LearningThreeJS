## Geometries
In THREE.JS, geometries are composed of vertices (point coordinates in 3D spaces) and faces (triangles that join those vertices to create a surface). 

We use geometries to create meshes, but you can also use geometries to form particles. Each vertex (singular of vertices) which will correspond to a particle(more on that on future).

We can store more data than the position in the vertices. A good example would be to talk about the UV coordinates or the normals.

---
## The different built-in geometries
Three.js has many built-in geometries. While you don't really need to know precisely how to instantiate each one; good to know them geometries exist.

All the built-in geometries that we're going to use will inherit from the #BufferGeometry class. This class has many built in methods like ``Translate(...)``, ``rotateX(...)`` ,``normalize()``, etc. 

Most of the geometries that the documentation pages have examples of:
- [BoxGeometry](https://threejs.org/docs/#api/en/geometries/BoxGeometry) To create a box.
- [PlaneGeometry](https://threejs.org/docs/#api/en/geometries/PlaneGeometry) To create a rectangle plane.
- [CircleGeometry](https://threejs.org/docs/#api/en/geometries/CircleGeometry) To create a disc or a portion of a disc (like a pie chart).
- [ConeGeometry](https://threejs.org/docs/#api/en/geometries/ConeGeometry) To create a cone or a portion of a cone. You can open or close the base of the cone.
- [CylinderGeometry](https://threejs.org/docs/#api/en/geometries/CylinderGeometry) To create a cylinder. You can open or close the ends of the cylinder and you can change the radius of each end.
- [RingGeometry](https://threejs.org/docs/#api/en/geometries/RingGeometry) To create a flat ring or portion of a flat circle.
- [TorusGeometry](https://threejs.org/docs/#api/en/geometries/TorusGeometry) To create a ring that has a thickness (like a donut) or portion of a ring.
- [TorusKnotGeometry](https://threejs.org/docs/#api/en/geometries/TorusKnotGeometry) To create some sort of knot geometry.
- [DodecahedronGeometry](https://threejs.org/docs/#api/en/geometries/DodecahedronGeometry) To create a 12 faces sphere. You can add details for a rounder sphere.
- [OctahedronGeometry](https://threejs.org/docs/#api/en/geometries/OctahedronGeometry) To create a 8 faces sphere. You can add details for a rounder sphere.
- [TetrahedronGeometry](https://threejs.org/docs/#api/en/geometries/TetrahedronGeometry) To create a 4 faces sphere (it won't be much of a sphere if you don't increase details). You can add details for a rounder sphere.
- [IcosahedronGeometry](https://threejs.org/docs/#api/en/geometries/IcosahedronGeometry) To create a sphere composed of triangles that have roughly the same size.
- [SphereGeometry](https://threejs.org/docs/#api/en/geometries/SphereGeometry) To create the most popular type of sphere where faces looks like quads (quads are just a combination of two triangles).
- [ShapeGeometry](https://threejs.org/docs/#api/en/geometries/ShapeGeometry) To create a shape based on a path.
- [TubeGeometry](https://threejs.org/docs/#api/en/geometries/TubeGeometry) To create a tube following a path.
- [ExtrudeGeometry](https://threejs.org/docs/#api/en/geometries/ExtrudeGeometry) To create an extrusion based on a path. You can add and control the bevel.
- [LatheGeometry](https://threejs.org/docs/#api/en/geometries/LatheGeometry) To create a vase or portion of a vase (more like a revolution).
- [TextGeometry](https://threejs.org/docs/?q=textge#examples/en/geometries/TextGeometry) To create a 3D text. You'll have to provide the font in typeface json format.

*Note to me: The shape terms kinda seem similar to blender :0 

If you need a particular geometry that is not supported by Three.js, you can create your own geometry in JavaScript, or you can make it in a 3D software, export it and import it inot your project (blender :3)

---

## Box example
We been playing around with cube but haven't really used the parameters. Most geometries have parameters, and always look at the documentation before using it.

The #BoxGeometry has 6 different parameters (again look documentation):
- width: The size on the x axis
- height: The size on the y axis
- depth: The size on the z axis
- widthSegments: How many subdivisions in the x axis
- heightSegments: How many subdivisions in the y axis
- depthSegments: How many subdivisions in the z axis

**IMPORTANT: Subdivisions correspond to how much triangles should compose the face. By default it's 1, meaning that there will only be 2 triangles per face. If you set the subdivision to 2, you'll end up with 8 triangles per face: 

```
const geometry = new THREE.BoxGeometry(1, 1, 1, 2, 2, 2)
//Adding the above code alone won't display the wireframe lol so you have to add the wireframe : true property to our material

const material = new THREE.MeshBasicMaterial({color : 0xff0000, wireframe : true})
```

It's going to show 8 triangles by face, but it's kind of not relevant for a flat face cube, it gets more interesting when using a SphereGeometry:

```
const geometry = new THREE.SphereGeometry(1,32,32) // radius, widthSegments, heightSegments(16+ is smoother)
```

*Note: The more subdivisions we add, the less we can distinguish the face. But always keep in mind that too many vertices and faces will affect performances.*

---

## Creating your own buffer geometry
Sometimes, we might need to create our own geometries. If the geometry is very complex or with a precise shape, it's better to create it in 3D software(blender hehe), but if the geometry isn't too complex, we can build it using #BufferGeometry.

To create your own buffer geometry, start by instantiating an empty #BufferGeometry . Creating a simple triangle:

```
// Create an empty BufferGeometry
const geometry = new THREE.BufferGeometry()
```

To add vertices to a #BufferGeometry , need to start with a #Float32Array.

#Float32Array are native JavaScript typed array. You can only store floats inside, and the length of that array is fixed. To create a #Float32Array , you can specify its length and then fill it later:

```
const positionsArray = new Float32Array(9)

// First vertice
positionsArray[0] = 0
positionsArray[1] = 0
positionsArray[2] = 0

// Second vertice
positionsArray[3] = 0
positionsArray[4] = 1
positionsArray[5] = 0

// Third vertice
positionsArray[6] = 1
positionsArray[7] = 0
positionsArray[8] = 0

```

Or can pass an array:

```
const positionsArray = new Float32Array([
	0,0,0, // First vertex
	0,1,0, // Second vertex
	1,0,0 // Third vertex
])
```

The coordinates of the vertices are specified linearly. The array is a one-dimensional array where you specify the x, y, and z of the first vertex, followed by the x, y, and z of the second vertex, and so on.

Before sending that array to the #BufferGeometry , you have to transform it into a #BufferAttribute.

The first parameter corresponds to your typed array and the second parameter corresponds to how much values make one vertex attribute. As we saw earlier, to read this array, we have to go 3 by 3 because a vertex position is composed of 3 values (x, y, and z):

```
const positionAttribute = new THREE.BufferAttribute(positionsArray, 3)
```

Then we can add this attribute to our #BufferGeometry using the setAttribute(...) method. The first parameter is the name of this attribute and the second parameter is the value:

```
geometry.setAttribuite('position', positionsAttribute)
```

We chose 'position' as the name because Three.js internal shaders will look for that value to position the vertices.(More in the future)

The faces will be automatically created following the order of the vertices.
All together:

```
// Creating an empty BufferGeometry
const geometry = new THREE.BufferGeometry()

// Creating a Float32Array containing the vertices position (3 by 3)
const positionsArray = new Float32Array([
	0,0,0, // First
	0,1,0, // Second
	1,0,0 // Third
])

// Creating the attribute and naming it 'position'
const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3)
geometry.setAttribute('position', positionsAttribute)
```

We can also create a bunch of random triangles:
```
// Creating an empty BufferGeometry
const geometry = new THREE.BufferGeometry()

// Creating 50 triangles (450 Values)
const count = 50
const positionsArray = new Float32Array(count * 3 * 3)
for(let i = 0; i < count * 3 * 3 < i++){
	positionsArray[i] = (Math.random() - 0.5) * 4
}

// Creating the attribute and naming it 'position'
const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3)
geometry.setAttribute('position', positionsAttribute)
```

*Note: Logic breakdown, We need 50 triangles so each triangle is composed of 3 vertices and each vertex is composed of 3 values(x, y, and z)*

---
## Index
One interesting thing with #BufferGeometry is that you can mutualize vertices using the index property. Consider a cube. Multiple faces can use some vertices like the ones in the corners. And if you look closely, every vertex can be used by various neighbor triangles. That will result in a smaller attribute array and performance improvement. 