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





