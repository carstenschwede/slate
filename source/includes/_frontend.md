
#Frontend

<aside class="warning">Please see the tutorial scene at <code>scenes/tutorial</code></aside>

```
node start frontend -s Tutorial
```

## Rooms

HoloR will load the room specified in `src/app.js` (defaults to AmiLab).

```
window.HoloR.loadRoom("AmiLab");
```

The room definition includes

- **size and room geometry** including walls, doors, furniture
- **projector configuration** (extrinsic and intrinsic parameters)

HoloR uses [three.js](https://github.com/mrdoob/three.js/) and WebGL to render objects.
The coordinate system

- is **right-handed, y-up**
- has one of the **lower room-corners** as its **origin**
- uses units in **meter**

## Loading scenes

The room only consists of walls, floor and loaded furniture.
To actually render something we need to load a scene.


## Creating new scenes
Follow the steps to create a new scene named "MyTestScene".

> Create a copy of the dummy template

```
cp -R HoloR/lib/scenes/Dummy HoloR/lib/scenes/MyTestScene
```

> Update the scene name at the end of `MyTestScene/index.js`

```
HoloRAbstractScene.add("MyTestScene",HoloRScene);
```

> Finally make sure you load the scene:

```
node start frontend -s MyTestScene
```

<aside class="notice">The terminal where you started <code>node start frontend</code> will show you errors if applicable (as will your browsers development console).</aside>


## Adding Objects
```javascript
	constructor() {

		//create a red Box with side-length 1m
		this.mesh = new THREE.Mesh(
			new THREE.BoxGeometry(1,1,1),
			new THREE.MeshBasicMaterial({
				color:0xff0000
			})
		);

		//assign it to this instance for later reference
		this.mesh = mesh;

		//add it to the scene so it gets rendered
		this.add(this.mesh);

		//to remove it:
		//this.remove(mesh);
	},

	onRender(now) {

		//animate x/z position based on current timestamp (now)
		let r = 3;
		let speed = 0.001;
		this.mesh.position.x = Math.sin(now*speed)*r;
		this.mesh.position.z = Math.cos(now*speed)*r;
	}
```

## Lights and Shadow
Please see the examples.

```javascript
	var light = new THREE.PointLight( 0x00ff55, 2, 50 );
	light.castShadow = true;
	light.position.set(1,2,1);
	this.addLight(light);
```

<aside class="notice">Make sure the objects in your scene have a material that responds to light, e.g. <code>MeshPhongMaterial</code> or <code>MeshLambertMaterial</code></aside>


## Textures

```javascript
	//load image
	var url = 'http://i.imgur.com/Gs04Gue.png';
	var texture = new THREE.TextureLoader().load(url);

	//attach image to 3d object
	var mesh = new THREE.Mesh(
		new THREE.BoxGeometry(1,1,1),
		new THREE.MeshBasicMaterial({
			map: texture
		})
	);
	this.add(mesh);
```
Support for

- images
- camera
- 2D canvas
- charts

- see `scenes/TextureExample`



## Keyboard/Mouse
```javascript

	//genering events
	var evtName = "keypress"; //keydown,keyup,keypress,click,dblclick
	this.on(evtName,evt => {
		console.log(evtName,"fired",evt);
	});

	//specific keys
	this.onKeyDown("k",() => {
		console.log("You pressed k");
	})
```

## Tracking Data
```javascript
onUpdate() {
	var viewer = HoloR.getNearestViewer();
	if (!viewer) return;

	//get viewer position and orientation
	console.log(viewer.position, viewer.quaternion);

	//access skeleton data
	console.log(viewer.skeleton.joints.Head);
}
```

## Camera
```javascript
THREE.ext.TextureCreator.camera({
	width: 1280,
	height: 720
},(err, cameraTexture) => {
	if (err) {
		return console.log(err);
	}

	var cube = new THREE.Mesh(
		new THREE.BoxGeometry(1,1,1),
		new THREE.MeshBasicMaterial({
			map: cameraTexture.texture
		})
	);

	this.add(cube);
});
```

## Audio

```javascript
const AudioTrack = require("plugins/SpatialAudio/AudioTrack.js");

//load audio sample
var audioTrack = new AudioTrack(this.resources("myAweSome.mp3"), {
	autoplay: false,
	loop: true
});

[...]

audioTrack.play();
audioTrack.pause();

```

## Spatial Audio

```javascript
const SpatialAudioPlugin = require("plugins/SpatialAudio");
const SpatialAudio = new SpatialAudioPlugin();
const AudioTrack = require("plugins/SpatialAudio/AudioTrack.js");

[...]

//load audio sample
var audioTrack = new AudioTrack(this.resources("myAweSome.mp3"), {
	autoplay: true,
	loop: true
});

var box = new THREE.Mesh(new THREE.BoxGeometry(1,1,1));
box.add(audioTrack);

[...]

```