# DEMO_KISA_XR_Starter

[todo] Brief description of the aim of the repo in the context of

- KISA master
- XR lecture


[todo] Brief description of Godot game engine and related work tutorials / links



## Hands on Godot

- Link to tutorials

- Forward vs mobile vs compatibility. Let's use mobile for Quest 2 / Quest 3

- Create a initial 3D scene that will hold all the project (main.tscn)

- Scenes vs Nodes. add node vs instantiate child scene. New scene vs new inherited scene

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













