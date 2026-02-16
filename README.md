# TUTORIAL_SCHELETON_KISA_XR_Starter

This repository is a **starter project for XR (VR/AR) development in Godot**, designed to support hands-on work in the context of the **KISA Master** and the accompanying **XR lecture/lab sessions**. The goal is to provide a clean, minimal baseline that gets you from new project to a working **room-scale XR scene** with an **XR rig, controller tracking, basic locomotion, and interaction**—and then gradually refine the setup using established community tooling.

The project uses the **Godot Engine**, a free and open-source game engine that supports both 2D and 3D development and provides a full editor workflow (scenes, nodes, scripting, export pipelines). For XR, we rely on **OpenXR**, the industry standard for cross-vendor XR runtimes, and Godot’s XR ecosystem plugins and toolkits to accelerate development of common interaction patterns (hands/controllers, teleportation, grabbing, movement, etc.).

### What you will build

By following this README, you will:

- Set up a Godot 3D project configured for XR (targeting standalone headsets like Meta Quest 2/3)
- Create a **main 3D scene** and modular sub-scenes (floor, rig, interactables)
- Add and configure an **XRRig** (origin, camera, controllers, hand models)
- Use **Godot XR Tools** functions for locomotion (snap/turn, direct movement, teleportation, jump)
- Implement **object interaction** (pickup/grab) and experiment with additional XR capabilities
- Export the project to **Android** for deployment on standalone XR headsets

### Godot and XR references (official + key repos)

**Godot (official)**  
- [Godot website](https://godotengine.org)  
- [Godot documentation](https://docs.godotengine.org)  
- [Godot source code (GitHub)](https://github.com/godotengine/godot)  

**XR in Godot (core ecosystem)**  
- [Godot XR Tools](https://github.com/GodotVR/godot-xr-tools) — toolkit used in this tutorial  
- [Godot OpenXR Vendors plugin](https://github.com/GodotVR/godot-openxr-vendors) — vendor extensions (e.g., Meta)  
- [Godot XR community/org](https://github.com/GodotVR)  

**OpenXR (standard)**  
- [Khronos OpenXR overview/spec](https://www.khronos.org/openxr/)  

## Hands on Godot

This section gets the project structure in place and covers the core Godot concepts you’ll use throughout the XR part: **rendering setup**, **scenes vs. nodes**, and **how to structure your project with reusable sub-scenes**.

### Godot official “first project” tutorials

If you’re new to the editor workflow (scenes, nodes, signals, running the project), follow at least one of these official tutorials:

- [Your first 2D game (official Godot docs)](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/index.html)
- [Your first 3D game (official Godot docs)](https://docs.godotengine.org/en/stable/getting_started/first_3d_game/index.html)

Even though this repo is XR/3D, the 2D tutorial is still useful for learning the editor basics and scripting patterns.

### Rendering mode for standalone headsets (Quest 2 / Quest 3)

Godot offers different renderer options. For standalone Android-based headsets like **Meta Quest 2 / Quest 3**, prefer the **Mobile** renderer to keep performance predictable on headset hardware.

- Use **Mobile** for this starter project (good balance of features and performance for Quest).
- Use **Forward+** mainly for desktop-class GPUs / PCVR and higher-end visuals.
- Use **Compatibility** when you need maximum hardware support (older GPUs / strict constraints), at the cost of modern rendering features.

> Tip: Pick the renderer early. Switching later can be done, but it may require re-checking materials, lighting, and project settings.

### Create the main 3D scene (`main.tscn`)

Create an initial 3D scene that will act as the root of the experience:

1. Create a new scene and choose **Node3D** as the root.
2. Save it as `main.tscn`.
3. Set it as your main scene:
   - **Project → Project Settings → Application → Run → Main Scene** → select `main.tscn`

From here, `main.tscn` will be responsible for “composing” the XR experience by instantiating sub-scenes (floor, rig, interactables, etc.).

### Scenes vs Nodes (the mental model)

In Godot, everything you build is a tree of **nodes**, saved as a **scene** (`.tscn`):

- **Node**: a single building block (transform, mesh, camera, script, light, collider, etc.).
- **Scene**: a saved node tree that can be reused, instanced, and composed into larger scenes.

Two common ways to grow your scene:

- **Add Node**: you place a new node directly in the current scene tree (good for one-off elements or quick prototypes).
- **Instantiate Child Scene**: you reuse a prebuilt scene inside another scene (best for modularity and reuse).

For this repo, prefer **instantiating child scenes** for the XR rig, floor, and interactables so the project stays clean and reusable.

### New scene vs inherited scene

Godot gives you two main ways to create reusable building blocks:

- **New Scene**: you build a scene from scratch (best when you’re designing your own components).
- **New Inherited Scene**: you create a scene that extends an existing one (best when customizing template scenes from libraries like *Godot XR Tools*).

In this project, you’ll typically:
- create **new scenes** for your own content (e.g., `floor.tscn`, `rig.tscn`)
- create **inherited scenes** when customizing XR Tools templates (e.g., a custom pickable object derived from XR Tools’ `pickable.tscn`)


## Hands on XR

1) Godot engine related configurations
- Enable XR in project settings 
- Enable XR shaders in project seetings
- Restart

2) Incorporating libraries
- Add XR toolkit libraries for Godot XR in Asset Library:
   - Godot OpenXR Vendors plugin (vendors)
   - Godot XR tools for Godot (godot-xr-tools)
- Restart

3) Enable libraries
- Godot does not enable libraries by default
- Go to project settings, check the plugins tab and enable Godot-XR-Tools
- Restart

- Explore the assets library and check all the content from the libraries


## Create the floor Scene

Create the following hierarchy: StaticBody3D -> CollisionShape3D -> MeshInstance3D and add a cube mesh of the desired size. 

This will be the floor for our XR experience. Add it to the main scene, either as a new subscene or directly as nodes in the main scene

Move the floor to -0.05 on y, so that the XRRig will be on top of it


## Create the XRRig Scene

Create a new scene that will hold the XRRig.

The base node must be XROrigin3D and must have a XRCamera3D as a child and also 2 XRController3D nodes

For the XRController3D assign correct tracker to each hand

Add the XR rig scene to the main scene and place it above the floor

Now we will add a script to the main scene to prepare the scene for VR. This is a näive approach that will be updated afterwards. Add the following code to the main scene (attach script)

```
extends Node3D

var xr_interface : XRInterface

func _ready():
  xr_interface = XRServer.find_interface("OpenXR")
  
  if xr_interface and xr_interface.is_initialized():
    print("OpenXR is initialized successfully :)")
    
    DisplayServer.window_set_vsync_mode(DisplayServer.VSYNC_DISABLED)
    
    get_viewport().use_xr = true
    
  else:
    print("OpenXR is not initialized. Please check HMD is working correctly :(")
```

At this point, we are in the position to test our experience on the HMD device. But we will not see much as we have not added 3D models for the hands.
Let's add some hand models by instantiating a child scene for the godot XR toolkit library.
Hands can be found under godot-xr-tools/hands path.

Add a low poly hand under the left controller and another one under the right controller


## Add some lighting

Hurrey! Hands and Camera3D should be moving now. The repo has only been tested on Meta Quest 2 and Meta Quest 3 though. Check possible problems for Pico and other devices.
Let's add some lighting to improve the scene.

Adding simple lighting in Godot is trivial. Go to Sun and environment settings and select add sun and add environment to it.
Note that a DirectionalLight3D and a WorldEnvironment node will be added in the main.tscn main scene.

## Let's move

In order to be able to move we just need to incorporate some functionality coded in the functions of the Godot-XR-Tools library.
Go the the scene in which we have defined the XRRig and instantiate a child scene for the left and right controller.

Movement is added instantiating a child scene called "MovementDirect", for instance we can add this to the left controller node.
Inspect the properties of the movement, try alternating strafe for instance.

Please note that this instantiation needs to incorporate a PlayerBody node to the scene, if not please restart Godot.

Instantiate movement turn scene as a subscene for the right controller.


## Let's refine

Now that our VR experience is getting serious, let's stick to the Godot XR Tools library for the preparation of the VR scene.
Remove our script coded previously (comment or detach the script), and instantiate the StartXR node in the main scene.
This will take care of XR preparations in higher detail for us.
You can check now all the additional configuration shown in the terminal when starting an XR Session


## Teleportation

Teleportation is a very handy movement in XR to move fast, its implementation in Godot is very simple.
Teleportation in Godot is provided by a function that must be instantiated in the controller we want to use to teleport.
Add a child node FunctionTeleport to the XRController3D node that will be used to teleport.


## Jump in VR

Jump is another function implemented by functions, its implementation in Godot is very simple.
Jumps in Godot are provided by a function that must be instantiated in the controller we want to use to jump.
Add a child node FunctionJump to the XRController3D node that will be used to jump.
Just be careful not to overlap buttons of the controllers for different actions. 
Let's say we want to jump by pressing A (ax_button) on the XRController3D, then instantiate the MovementJump on the controller and select the trigger key.
Triggers keys can be checked on the XR action map menu, on the bottom side of Godot editor.

To edit the jump strength, you may need to inspect the PlayerBody3D node and create a physics instance.
Among those options jump strength can be tuned (jump velocity and jump max slope).


## Picking up objects

The functionality to pick up objects is also provided by instantiating a function from Godot XR Tools.
For both XRController3D nodes instantiate the FunctionPickup node.

To debug the experience, obviously we will need to create some pickable objects derived from the pickable generic scene from Godot XR Tools.
For that task, create a inherited pickable scene (addons/godot-xr-tools/objects/pickable.tscn) that will be composed of the following hierarchical elements: 
PickableObject->CollisionShape3D->MeshInstance3D and create some funny shapes to pick-up. Do not forget to provide the collision shape or the object will not interact with grabbing.
Add pickable elements to the main scene in order to test grabbing.

Grab points for objects can also be added.
Create a new inherited scene derived from the previous pickable object, and add GrabPointHandLeft and GrabPointHandRight nodes to it. 
You can visualize the grabbing hand and tune it as desired. For the task you will need to create a new XRToolsHandPose attribute a load a new animation available in hands/animations.
Disable the snapping option in the inspector (snap hand), don't really know why it does not work.



## Try additional movement functions to the XRController3D class

There are a lot of built-in functions to increase functionality of the controllers, just try adding / removing different functions instantiating functions at will


## Exporting to android / Meta Quest 2/3

Exporting the app to native android is a bit of a complex process (Don't worry it can be done! we did it, and so can you!). Please read and follow the latest official documentation.

Requirements are to install the following (do not install linux distribution packages, they tend to be outdated):

- install jdk-17 (tested with distribution openjdk17)
- install android related things: easiest is to do the full install of android studio, and then relevant tools and sdk. Go to Settings -> Language & Frameworks -> install everything from SDK tools, and latest from SDK platforms
- install adb

Then move to Godot

- Install the export templates from Godot (Editor -> manage export templates)
- Install the android templates from Godot (Project -> android build templates)

Finally export to android:

- Build with Graddle
- Check OpenXR backed
- Select architecture arm-64
- Enable Meta plugin (vendors library must be installed)
- 


Check available usb devices with sudo adb devices





