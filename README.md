# Potree Core

 - This project is based on Potree V1.6.
 - Potree is a project created by Markus Schütz i have only adapted some of its components.
 - Some elements were removed from the library
 	- PotreeViewer
 	- Controls, Input, GUI, Tools
 	- Anotations, Actions, ProfileRequest
 	- Potree.startQuery, Potree.endQuery and Potree.resolveQueries
 	- Potree.timerQueries
 	- Potree.MOUSE, Potree.CameraMode
 	- PotreeRenderer, RepRenderer, Potree.Renderer
	- JQuery, TWEEN and Proj4 dependencies
 - Live example at https://tentone.github.io/potree-core/

### How to use
 - Download the custom potree build from the build folder
 - Include it alonside the worker folder in your project
 - Download threejs from github repository
    - https://github.com/mrdoob/three.js/tree/dev/build
 - The build provided its not a module, its a bundle, it requires ES6 feature level support

### Example

 - Bellow its a fully functional example of how to use this wrapper to load potree point clouds to a THREE.js project

```javascript
var scene = new THREE.Scene();
var camera = new THREE.PerspectiveCamera(60, 1, 0.1, 10000);

var canvas = document.createElement("canvas");
canvas.style.position = "absolute";
canvas.style.top = "0px";
canvas.style.left = "0px";
canvas.style.width = "100%";
canvas.style.height = "100%";
document.body.appendChild(canvas);

var renderer = new THREE.WebGLRenderer({canvas:canvas});

var geometry = new THREE.BoxGeometry(1, 1, 1);
var material = new THREE.MeshBasicMaterial({color: 0x00ff00});
var cube = new THREE.Mesh(geometry, material);
scene.add(cube);

var controls = new THREE.OrbitControls(camera, canvas);
camera.position.z = 10;

var points = new Potree.Group();
points.setPointBudget(10000000)
scene.add(points);

Potree.loadPointCloud("data/test/cloud.js", name, function(data)
{
	var pointcloud = data.pointcloud;
	points.add(pointcloud);
});

function loop()
{
	controls.update();
	renderer.render(scene, camera);
	requestAnimationFrame(loop);
};
loop();

document.body.onresize = function()
{
	var width = window.innerWidth;
	var height = window.innerHeight;
	renderer.setSize(width, height);
	camera.aspect = width / height;
	camera.updateProjectionMatrix();
}
document.body.onresize();
```


### Reference
 - The project has no generated documentation but bellow are some of the main configuration elements
 - Potree.BasicGroup
    - Container that stores point cloud objects and updates them on render.
    - The container supports frustum culling using the point cloud bouding box.
    - Automatically stops updating the point cloud if out of view.
    - This container does not support all features but its faster.
 - Potree.Group
    - Complete container with support for all potree features.
    - Some features might require support for the following GL extensions
       - EXT_frag_depth
       - WEBGL_depth_texture
       - OES_vertex_array_object
 - Potree.loadPointCloud
    - Method to load a point cloud database file
    - Potree.loadPointCloud(url, name, onLoad)
 - Potree.PointCloudMaterial
    - Material used by threejs to draw the point clouds, based on RawShaderMaterial
    - shape
    - Defines the shape used to draw points
       - Possible values are
          - Potree.PointShape.SQUARE
          - Potree.PointShape.CIRCLE
          - Potree.PointShape.PARABOLOID
    - pointSizeType
       - Defines how the point cloud points are sized, fixed mode keeps the same size, adaptive resizes points accordingly to their distance to the camera 
       - Possible values are
          - Potree.PointSizeType.FIXED
          - Potree.PointSizeType.ATTENUATED
          - Potree.PointSizeType.ADAPTIVE
    - pointColorType
       - Defines how to color the drawn points
       - Possible values are
          - Potree.PointColorType.RGB
          - Potree.PointColorType.COLOR
          - Potree.PointColorType.DEPTH
          - Potree.PointColorType.HEIGHT
          - Potree.PointColorType.INTENSITY
          - Potree.PointColorType.INTENSITY_GRADIENT
          - Potree.PointColorType.LOD
          - Potree.PointColorType.POINT_INDEX
          - Potree.PointColorType.CLASSIFICATION
          - Potree.PointColorType.RETURN_NUMBER
          - Potree.PointColorType.SOURCE
          - Potree.PointColorType.NORMAL
          - Potree.PointColorType.PHONG
          - Potree.PointColorType.RGB_HEIGHT
    - weighted
       - If true points are drawn as weighted splats
    - treeType
       - Defines the type of point cloud tree being drawn by this material
       - This should be automatically defined by the loader
          - Potree.TreeType.OCTREE
          - Potree.TreeType.KDTREE
 - Potree.PointCloudTree
    - Base Object3D used to store and represent point cloud data.
    - These objects are created by the loader

### To do
 - Point cloud group raycast support
 - Recaulate bouding box for frustum culling
 - Fix "THREE.Camera: .getWorldDirection() target is now required" warning
 - Support for logarithmic depth buffer
 
### Building
 - The output javascript is not a module of any kind
 - The project can be build using
    - npm install
    - npm run build

### Create Point Clouds
 - Use the Potree Converter tool to create point cloud data from LAS, ZLAS or BIN point cloud files
    - https://github.com/potree/PotreeConverter/releases
 
### Dependencies
 - three.js
 - Closure compiler
