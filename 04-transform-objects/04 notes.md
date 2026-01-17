### Transformation

**Properties to transform objects in our scene**
- Position(To move an object)
- Scale(To resize an object)
- Rotation(To rotate the object)
- Quaternion(To also rotate the object; more later)

All classes that inherit from the Object3D classes possess those properties like PerspectiveCamera or Mesh and class that we haven't covered yet.

##### Moving objects
The position possesses three essential properties, which are x, y, and z. Those are the three necessary axes to position something in a 3D space. 

The direction of each axis is purely arbitrary, and it can vary accordingly to the environment. In THREE.JS we usually consider that the y-axis is going upwards, the z-axis is going backwards, and the x-axis is going to the right.

As for the unit, it's totally up to us. 1 can be 1 cm, 1 m or even 1 km. Just need to adapt the unit to what you want to build. Example being, if you want to create a house think 1 unit as 1 meter. Also, make sure to change the property before the render(....) method,

```
mesh.position.x = 0.7
mesh.position.y = -0.6
mesh.position.z = 1
```

*Notes:* The position property is not any object. It's an instance of Vector3 class. While the class has an x, y, and z property, it also has many useful methods.

**Getting the length of a vector**:

```
console.log(mesh.position.length())
```

**Can get the distance from another Vector(make sure to use this after creating the camera)**:

```
console.log(mesh.position.distanceTo(camera.position))
```

**We can also normalize its value(meaning that you will reduce the length of the vector to 1 unit but preserve its direction)**:

```
console.log(mesh.position.normalize())
```

**To change the values, instead of changing x, y, and z separately, you can also use the set(...) method:**

```
mesh.position.set(0.7, -0.6, 1)
```


##### Axes Helper:
Positioning things in space can be real challenging, knowing where each axis is oriented is complicated especially when we start to move the camera.

One good solution is to use THREE.JS AxesHelper.

AxesHelper will display three lines corresponding to the x, y, and z axes each one starting at the center of the scene and going in the corresponding direction. To create the AxesHelper, instantiate it and add it to the scene right after instantiating that state. You can specify the length of the lines as the only parameter:

```
// AxesHelper
const axesHelper = new THREE.AxesHelper(2)
scene.add(axesHelper)
```

##### Scale Objects:
Scale is also a Vector3. By default x, y, and z are equal to 1. Meaning that the object has no scaling applied.

If you put 0.5 as a value, the object will be half of its size on this axis, and if you put 2 as a value, it will be twice its original size on this axis.

```
mesh.scale.x = 2
mesh.scale.y = 0.25
mesh.scale.z = 0.5
```

##### Rotate Objects
Rotation is a little more troublesome than position and scale. There are two ways of handling rotation.

1. Self-evident rotation property
2. Quaternion property

THREE.JS supports both and updating one will update another automatically.