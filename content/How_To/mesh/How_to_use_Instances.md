---
ID_PAGE: 22571
PG_TITLE: How to use Instances
---

# How to use Instances
Instances are an excellent way to use hardware accelerated rendering to draw a huge number of identical meshes (let's imagine a forest or an army).

Instances are built from a mesh with the following code:

```javascript
// In this case we're loading our mesh from an external source.
BABYLON.SceneLoader.ImportMesh("", "//www.babylonjs.com/assets/Tree/", "tree.babylon", scene, function (newMeshes) {
    var mesh = newMeshes[0];
    // Make the "root" mesh not visible. The instanced versions of it that we
    // create below will be visible.
    mesh.isVisible = false;
    for (var index = 0; index < 100; index++) {
        var newInstance = mesh.createInstance("i" + index);
        // Here you could change the properties of your individual instance,
        // for example to form a diagonal line of instances:
        //  newInstance.position.x = index;
        //  newInstance.position.z = index;
        // See below for more details on what can be changed.
    }
});
```
A mesh can have as many instances as you want.

Each instance has the same material as the root mesh. They can vary on the following properties:
* ```position```
* ```rotation```
* ```rotationQuaternion```
* ```setPivotMatrix```
* ```scaling```

# Custom buffers

You also have the opportunity to specify per instance values for any attribute. For instance (no pun intended), if you want to have a specific color per instance, you only need to provide a vertex buffer flagged as "instanceable" and fill it with a color per instance:

```
let instanceCount = 1000;
let colorData = new Float32Array(4 * instanceCount);

for (var index = 0; index < instanceCount; index++) {
    colorData[index * 4] = Math.random();
    colorData[index * 4 + 1] = Math.random();
    colorData[index * 4 + 2] = Math.random();
    colorData[index * 4 + 3] = 1.0;
}

var buffer = new BABYLON.VertexBuffer(engine, colorData, BABYLON.VertexBuffer.ColorKind, false, false, 4, true);
box.setVerticesBuffer(buffer);
```

The last parameter of the VertexBuffer constructor is the one to set to true to flag it as instanceable.

Example: https://www.babylonjs-playground.com/#8L50Q3#1

The other way is to register a custom buffer with `registerInstancedBuffer`:
```
mesh.registerInstancedBuffer("color", 4); // 4 is the stride size eg. 4 floats here
```

Using this API, you can specify a new buffer that will be instancied. This means that each instance will have its own value. You can specify this value on the root mesh and on every single instance:

```
box.instancedBuffers.color = new BABYLON.Color4(Math.random(), Math.random(), Math.random(), 1);
let instance = box.createInstance("box1");
instance.instancedBuffers.color = new BABYLON.Color4(Math.random(), Math.random(), Math.random(), 1);
```

The system will take care of updating the internal vertex buffer.

Example: https://www.babylonjs-playground.com/#YPABS1

# Support
Instances are supported for collisions, picking, rendering and shadows. Even if the current hardware does not support hardware accelerated instances, babylon.js will be able to optimize rendering to take instances into account.

# Using Blender to create instances
Using Blender, you can create instances of a mesh by just creating a linked object:

![](https://www.html5gamedevs.com/uploads/monthly_05_2014/post-7026-0-82151000-1401073383.jpg)

# Using 3DS Max to create instances
Using 3DS Max, you can create instances of a mesh by just creating a clone instance object with clic right on the object:

![](https://www.html5gamedevs.com/uploads/monthly_11_2014/post-5292-0-54659600-1415793353.jpg)

# Limitations
You can use instances with LOD but one limitation will apply in this case: You will have to hide the root objects.
Here is an example where LODs reuse instances:
https://www.babylonjs-playground.com/#0720FC#10

If you want to create an instance from a cloned mesh, you have to first make sure that you call clonedMesh.makeGeometryUnique().

# Demo
https://www.babylonjs-playground.com/#YB006J#75
