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

This section prepares the project for XR using **OpenXR** and the **Godot XR Tools** ecosystem. The goal is to (1) enable XR support in the engine, (2) install the required addons, and (3) activate the plugins so Godot can use them at runtime.

### 1) Godot engine configurations (XR project settings)

1. Open **Project → Project Settings**.
2. Enable XR support:
   - Search for **XR** and enable **XR / OpenXR**-related options as needed for your Godot version.
3. Enable XR shaders:
   - Search for **XR shaders** and enable the XR shader option (required for correct rendering on XR runtimes).
4. **Restart Godot** after changing these settings.

> Notes:
> - Godot applies some XR and rendering changes only after restarting the editor.
> - If you later change renderer settings or XR-related rendering settings, restart again to avoid “it should work but doesn’t” situations.

### 2) Incorporating libraries (Asset Library)

Godot XR workflows typically use community addons for common interaction patterns and vendor extensions.

1. Go to **AssetLib** (top tab in the editor).
2. Search and install the following addons:
   - **Godot OpenXR Vendors plugin** (often named *OpenXR Vendors* or similar)  
     This provides vendor-specific OpenXR extensions (e.g., Meta features) when available.
   - **Godot XR Tools** (*godot-xr-tools*)  
     This provides reusable XR building blocks (“functions”) such as locomotion, grabbing, hand poses, and helper nodes.

3. After installing, **restart Godot**.

### 3) Enable libraries (Plugins tab)

Installing an addon does not always enable it automatically.

1. Go to **Project → Project Settings → Plugins**.
2. Enable:
   - **Godot XR Tools** (toggle it to *On*)
   - (Optionally) any vendor plugin you intend to use (e.g., Meta extensions via the Vendors plugin)

3. **Restart Godot** after enabling plugins.

### 4) Explore what you installed

Before you start wiring things into your own scenes, take a few minutes to explore:

- The `addons/` folder (FileSystem panel) to see what each library provides.
- **XR Tools** scenes and prefabs (hands, movement, teleport, pickup, etc.).
- Any demo scenes, example assets, and documentation files included with the addons.

This will make it easier to recognize what to **instantiate as child scenes** later (instead of re-building features from scratch).



## Create the floor Scene

Create the following hierarchy: `StaticBody3D → CollisionShape3D → MeshInstance3D` and add a **BoxMesh** (cube) of the desired size.

This will be the floor for our XR experience. Add it to the main scene, either as a new subscene or directly as nodes in the main scene.

Move the floor to `y = -0.05`, so that the XRRig will be on top of it.

> **Tip (floor textures):** If you want a clear tactical reference texture for scale and orientation (grid lines, tiles, etc.), you can generate one quickly using the **Texture Grid Generator**:  
> [https://wahooney.itch.io/texture-grid-generator](https://wahooney.itch.io/texture-grid-generator)

## Create the XRRig Scene

Create a new scene that will contain the player XR rig (origin, camera, and controllers).

### 1) Build the XRRig node hierarchy

1. Create a **new scene**.
2. Set the root node to **`XROrigin3D`**.
3. Add the following child nodes under `XROrigin3D`:
   - **`XRCamera3D`**
   - **`XRController3D`** (Left hand)
   - **`XRController3D`** (Right hand)

Your scene tree should look like this:

- `XROrigin3D`
  - `XRCamera3D`
  - `XRController3D` (Left)
  - `XRController3D` (Right)

Save this scene as something like `xr_rig.tscn` (name it consistently with your project structure).

### 2) Assign the correct trackers (left/right)

For each `XRController3D`, set the tracker/hand assignment in the Inspector:

- Left controller: set it to **Left Hand**
- Right controller: set it to **Right Hand**

(Exact property names can vary slightly by Godot version, but you are looking for a field that identifies the controller as left/right.)

### 3) Instance the rig into the main scene

1. Open `main.tscn`.
2. **Instantiate** the XR rig scene (`xr_rig.tscn`) as a child of your main root node.
3. Place the rig slightly above the floor so you start standing on it (you can tweak the height later).

### 4) Naive XR initialization script (temporary)

Next, we’ll add a small script to `main.tscn` to start an OpenXR session.  
This is a **naive** approach and we will replace/refine it later using the XR Tools startup helper node.

Attach a script to the root node of `main.tscn` and add the following code:

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

> For the full XR workflow, concepts, and engine-specific details, refer to the official Godot XR docs:  
> - [Godot XR documentation](https://docs.godotengine.org/en/stable/tutorials/xr/index.html)

At this point, we are in a position to test the experience on the HMD. However, you won’t see much yet because the controllers don’t have any visible models attached.

### Add hand models (XR Tools)

We’ll use the ready-made hand scenes from **Godot XR Tools**:

1. In the **FileSystem** panel, locate the XR Tools hands under:
   - `addons/godot-xr-tools/hands/`

2. For the **left** `XRController3D`:
   - **Instantiate** a low-poly hand scene as a child of the left controller (e.g., the left low-poly hand from the hands folder).

3. For the **right** `XRController3D`:
   - **Instantiate** the corresponding low-poly hand scene as a child of the right controller.

Your rig should now look roughly like this:

- `XROrigin3D`
  - `XRCamera3D`
  - `XRController3D` (Left)
    - `…Hand…` (low poly)
  - `XRController3D` (Right)
    - `…Hand…` (low poly)

Run on your headset again: you should now see the hands/controllers moving with your tracked input.


## Add some lighting

Hooray! Your hands and `XRCamera3D` should be moving now. This repo has been tested on **Meta Quest 2** and **Meta Quest 3**; for other headsets (e.g., Pico), you may need to check OpenXR runtime/device-specific compatibility and plugin support.

Let’s add some basic lighting to make the scene readable.

### Add a Sun + Environment

In Godot, a simple setup is usually enough:

1. Open `main.tscn`.
2. In the top toolbar, go to **Sun & Environment**.
3. Click:
   - **Add Sun** → creates a `DirectionalLight3D`
   - **Add Environment** → creates a `WorldEnvironment`

After this, your `main.tscn` scene tree should include something like:

- `DirectionalLight3D`
- `WorldEnvironment`

You can tweak intensity, rotation (sun direction), and environment settings later, but even the defaults should make the scene much clearer in-headset.


## Let’s move

To add locomotion, we’ll reuse the ready-made **movement functions** provided by **Godot XR Tools**. These are packaged as scenes you can **instantiate as children** under each controller.

### 1) Add XR Tools movement scenes to the controllers

1. Open your XR rig scene (`xr_rig.tscn`).
2. Select the **left** `XRController3D` node and **instantiate** the XR Tools movement scene:
   - **`MovementDirect`** (from the XR Tools movement/function scenes)

This adds joystick/thumbstick-based movement. Once instanced:

- Inspect the `MovementDirect` node in the Inspector
- Try options such as **strafe** (side movement) or other movement settings depending on the template

3. Select the **right** `XRController3D` node and **instantiate** a turning movement scene:
   - e.g., **`MovementTurn`** (or the equivalent XR Tools turning scene available in your version)

This adds right-stick turning (snap or smooth depending on settings).

### 2) Make sure `PlayerBody` is present

XR Tools movement typically requires a **player body** node in the rig setup (often named `PlayerBody` / `PlayerBody3D` depending on the XR Tools version).

- If adding `MovementDirect` doesn’t automatically add or hook into a `PlayerBody`, try **restarting Godot** and re-check the scene.
- Confirm your rig contains the required player body node so locomotion has something to move.

After this, running on the headset should allow:
- **move** using the left controller input (direct movement)
- **turn** using the right controller input (turn movement)



## Let’s refine

Now that the VR experience is getting serious, we’ll rely on **Godot XR Tools** to handle XR startup in a cleaner and more robust way.

### Replace the “naive” XR init script with `StartXR`

1. Open `main.tscn`.
2. **Disable** the temporary XR initialization script you added earlier:
   - either **comment it out**, or **detach the script** from the root node.
3. Add XR Tools startup:
   - **Instantiate** the XR Tools node/scene called **`StartXR`** into `main.tscn` (as a child of the root node).

`StartXR` takes care of XR session setup and related configuration in more detail than our minimal script (e.g., initialization flow, logging, and XR-specific viewport preparation).

### Verify in the output/terminal

Run the project again and look at the editor output / terminal logs.  
You should see additional XR session configuration messages printed when the XR session starts—this helps confirm XR Tools is now managing startup correctly.



## Teleportation

Teleportation is a very handy locomotion method in XR for fast movement with minimal discomfort. In **Godot XR Tools**, teleport is implemented as a **function scene/node** that you add to the controller you want to use.

### Add `FunctionTeleport` to a controller

1. Open your XR rig scene (`xr_rig.tscn`).
2. Decide which controller will handle teleportation (commonly the **right** controller).
3. Select that `XRController3D` node and **instantiate** the XR Tools teleport function as a child:
   - **`FunctionTeleport`**

Your controller subtree should end up roughly like:

- `XRController3D` (e.g., Right)
  - `FunctionTeleport`

Run the project on the headset and test teleportation using the controller input configured by XR Tools (you can adjust bindings and behavior via the node’s Inspector properties and the XR Action Map).


## Jump in VR

Jumping is another locomotion feature provided by **Godot XR Tools** as a function you attach to a controller.

### Add jump to a controller

1. Open your XR rig scene (`xr_rig.tscn`).
2. Choose which `XRController3D` will control jumping.
3. **Instantiate** the XR Tools jump function as a child of that controller:
   - `FunctionJump` (or the equivalent jump function scene in your XR Tools version)

> **Button conflicts:** Be careful not to bind multiple actions to the same controller button (e.g., teleport + jump on the same input).

### Example: bind jump to **A** (`ax_button`)

If you want to jump by pressing **A** (often exposed as `ax_button`):

1. Select the jump function node you added (e.g., `FunctionJump`).
2. In the Inspector, set the **trigger / activation input** to `ax_button` (or the matching action in your Action Map).
3. If you’re unsure which inputs are available, open the **XR Action Map** panel (bottom area of the editor) and inspect the actions/bindings.

> Depending on XR Tools version, the node might be named `FunctionJump` while the underlying movement behavior may appear as `MovementJump` or similar. Use the node’s Inspector to set the activation input either way.

### Tune jump strength (Player body physics)

Jump behavior is typically applied through the player body node (often `PlayerBody3D`):

1. Locate the player body in your rig (e.g., `PlayerBody` / `PlayerBody3D`).
2. Ensure it has the required **physics/character configuration** (some setups require creating or enabling a physics instance/component).
3. Tune jump-related parameters such as:
   - **Jump velocity / strength**
   - **Max slope** (affects how jumps and grounding behave on inclines)

After tuning, test in-headset and adjust until the jump feels comfortable and predictable.


## Picking up objects

Object grabbing in **Godot XR Tools** is done by adding a pickup “function” to your controllers, and making objects **pickable** by inheriting from the XR Tools pickable template.

### 1) Enable grabbing on both controllers

1. Open your XR rig scene (`xr_rig.tscn`).
2. For **each** `XRController3D` (Left and Right), **instantiate** the XR Tools pickup function as a child:
   - `FunctionPickup`

You should end up with something like:

- `XRController3D` (Left)
  - `FunctionPickup`
- `XRController3D` (Right)
  - `FunctionPickup`

### 2) Create a custom pickable object (inherited scene)

To test grabbing, we need objects that can be picked up.

1. In the FileSystem, locate the XR Tools pickable template scene:
   - `addons/godot-xr-tools/objects/pickable.tscn`

2. Create an **inherited scene** from it:
   - Right click `pickable.tscn` → **New Inherited Scene**
   - Save it as something like `pickable_custom.tscn`

3. In your inherited scene, build/confirm a simple visible + collidable hierarchy (example):

- `PickableObject`
  - `CollisionShape3D`
  - `MeshInstance3D`

4. Assign:
   - A mesh (cube, sphere, “funny shapes”, etc.) to `MeshInstance3D`
   - A matching collision shape to `CollisionShape3D`

> **Important:** If you forget the `CollisionShape3D`, the object won’t properly interact with grabbing.

5. Instance a few of these pickable objects into `main.tscn` so you can test grabbing in the headset.

### 3) Add grab points (optional, for better hand placement)

Grab points allow you to control how the hand aligns when grabbing an object.

1. Create another **inherited scene** derived from your custom pickable object (e.g., `pickable_with_grabpoints.tscn`).
2. Add:
   - `GrabPointHandLeft`
   - `GrabPointHandRight`

3. To visualize and tune the grabbing pose, configure a hand pose:
   - Create a new `XRToolsHandPose` resource/attribute
   - Load an animation from the XR Tools hand animations folder, typically under:
     - `addons/godot-xr-tools/hands/animations/`

4. In the GrabPoint inspector settings:
   - Disable **Snap Hand** (`snap_hand`) if snapping is causing issues in your setup.

Now you can test:
- natural grabbing with `FunctionPickup`
- improved alignment using `GrabPointHandLeft/Right` and hand pose animations


## Try additional movement functions on `XRController3D`

Godot XR Tools provides many extra **function scenes** you can attach to an `XRController3D` to extend what the controller can do (locomotion, interaction helpers, UI pointers, etc.). The workflow is always the same:

1. Select an `XRController3D` node (Left or Right).
2. **Instantiate** an XR Tools function scene as a **child** of that controller.
3. Configure it in the Inspector (inputs, behavior, parameters).
4. Test in-headset, then iterate (swap functions in/out as needed).

### Where to find functions

Browse the XR Tools addon folder in the FileSystem panel:

- `addons/godot-xr-tools/`

Look for folders that contain “functions” (commonly named `functions/`, `movement/`, `objects/`, etc., depending on XR Tools version). Most reusable controller features are provided as scenes you can instance.

### Suggested experiments

Try mixing and matching features to understand how XR Tools composes behavior:

- Swap **direct movement** for **teleport-only** locomotion and compare comfort
- Try different **turning** styles (snap vs smooth if available)
- Combine **pickup** with different grab-point setups and hand poses
- Adjust movement parameters (speed, acceleration, deadzones) and see how it feels in XR

> Tip: Add one function at a time and test. If something breaks (missing dependencies like `PlayerBody`), undo the last change, restart Godot, and re-check the scene tree for required nodes.


## Exporting to Android / Meta Quest 2/3

Exporting to Android (and deploying to Meta Quest devices) has a few moving parts. Don’t worry—once your toolchain is set up, iteration becomes straightforward. **Always follow the latest official Godot documentation** for Android export, as requirements can change between versions.

### Requirements (install on your machine)

> Avoid installing Android tooling from Linux distribution packages when possible—they can be outdated compared to what Godot expects.

- **JDK 17**
  - Install **JDK 17** (e.g., `openjdk-17` on many systems).
- **Android SDK + tools**
  - Easiest path: install **Android Studio**, then install the required SDK components:
    - Android Studio → **Settings** → **Language & Frameworks** → **Android SDK**
    - Install the latest required items from **SDK Platforms**
    - Install the relevant items from **SDK Tools** (platform-tools are essential)
- **ADB**
  - Install Android Debug Bridge (**adb**) (often included via Android SDK Platform-Tools)

### Godot setup

1. **Install export templates**
   - **Editor → Manage Export Templates** → install matching templates for your Godot version
2. **Install Android build templates**
   - **Project → Install Android Build Template**

### Export settings (Android / Quest)

1. Go to **Project → Export…**
2. Add or select the **Android** export preset.
3. Configure the preset:
   - Enable **Gradle Build** (Build with Gradle)
   - Ensure **OpenXR** is enabled for XR builds (OpenXR backend)
   - Select **arm64-v8a** (ARM 64-bit) architecture
   - If targeting Meta Quest features, enable the **Meta / Vendors** plugin support
     - (Requires the OpenXR Vendors plugin to be installed and enabled)

### Deploy & verify device connection

Before exporting/running to device, confirm your headset is visible over USB:

```bash
adb devices
```

On Linux you may need sudo (depending on your setup), and you may need proper udev rules for Android devices.
If your headset shows up in the list, you’re ready to export and install the APK to the device.





