# RuntimeDrawPro Plugin Documentation

## 1. Introduction
**RuntimeDrawPro** is an advanced C++ plugin specifically designed for Unreal Engine 5 (UE5), with excellent Blueprint compatibility. Its primary function is to **conveniently and efficiently draw various geometric shapes, lines, and solids at runtime (including in packaged Shipping builds)**.

It solves the pain point where native UE nodes like `DrawDebugLine` and `DrawDebugSphere` automatically cease to function in Shipping builds. Based on the engine's underlying Primitive Draw Interface (PDI) and Dynamic Mesh Builder, this plugin is a powerful tool for gameplay programmers, technical artists, and GIS/Digital Twin developers.

---

## 2. Installation and Configuration

1. **Place the plugin**: Move the `RuntimeDrawPro` folder into the `Plugins` folder located in your project's root directory (create the folder manually if it does not exist).
2. **Enable the plugin**: Open Unreal Engine, go to the top menu bar, select `Edit` -> `Plugins`, search for `RuntimeDrawPro`, and check the box to enable it.
3. **Prerequisites**: This plugin relies on UE's official Geometry Scripting system. Please search for and enable the official **`Geometry Scripting`** plugin in the plugin list as well.
4. Restart the engine and wait for the code to compile.

---

## 3. Core Modules and Function Guide

All core features of the plugin are exposed to Blueprints, and you can directly search for the corresponding nodes in the Blueprint editor.

### 3.1 Intuitive Lifecycle Management (URuntimeDrawBFL)
When using this plugin to draw any shape, **there is no need to manually create or destroy Actors**. The plugin provides a global Blueprint Function Library (BFL) that manages memory intelligently via the `LifeTime` parameter:
* **`LifeTime = 0.0f`**: Single-frame drawing; performance cost is extremely low.
* **`LifeTime > 0.0f`**: The plugin automatically generates a lightweight, invisible Actor component in the background and **automatically destroys it** after the specified time.
* **`LifeTime < 0.0f`**: Permanent existence.

### 3.2 Wireframe Drawing (PDI Component)
Supports rendering over 15 types of hollow wireframe geometries directly in 3D space.
* **Supported Shapes**: WireSphere, WireBox, Rectangle, Circle, Arc, WireCapsule, WireCylinder, WireCone, WireChoppedCone, OrientedWireBox, WireStar, WireDiamond, ConnectedArrow, and Prism.
* **Common Parameter Explanations**:
  * `Color`: Line color.
  * `Thickness`: Line thickness.
  * `Depth Priority`: Determines if the line is obscured by other objects (higher values appear in front).
  * `Depth Bias`: Depth offset to resolve Z-fighting flicker.
  * `bScreenSpace`: When enabled, line thickness remains constant on screen regardless of camera distance.

### 3.3 Solid Drawing and Custom Materials (Solid Component)
The `URuntimeSolidComponent` offers a rendering solution for solid geometries and **supports custom Materials**, making it perfect for AOE skill indicators, energy shields, etc.
* **Supported Shapes**: SolidTriangle, SolidBox, SolidSphere, SolidCone, SolidCylinder, SolidCapsule, SolidPlane, SolidTorus, SolidDisc, etc.
* **Usage**: In the drawing node, simply pass your custom Material Instance into the `Material` pin.

### 3.4 Advanced Line and Point Set System (Line Component)
Designed for complex trajectory and marking requirements, supporting both continuous and batch rendering.
* **Lines (Continuous Polylines)**: Pass an array of world coordinates to instantly draw connected complex polygons or navigation paths.
* **Dashed Lines (Custom Dashed)**: Supports setting start/end points and adjusting the `Dash Size`.
* **Points (Point Cloud)**: Batch render points of specific pixel sizes in world space.

### 3.5 Exclusive GIS and Advanced Math Toolkit (GISBFL)
Includes a built-in `UGISBFL` Blueprint Function Library providing computational support for enterprise-grade applications and complex game logic:
* **Render Target Drawing**: Use the `DrawPolygonToTextureRenderTarget2D` node to dynamically triangulate any 2D polygon vertices and draw them directly to a Render Target—ideal for minimap masks or dynamic UI creation.
* **Geometry Calculation**: Includes nodes for calculating 2D/3D polygon area (`Compute2DArea` / `Compute3DArea`) and total polyline length (`ComputeLen`).
* **Terrain Snapping**: Using `GetGroundPointsForPath` or `GenerateLineTracePointsAlongPath`, you can automatically project paths onto uneven terrain by firing downward raycasts.

---

## 4. Common Application Scenarios
1. **Gameplay**: MOBA/RPG skill range indicators, RTS box selection, predictive trajectories for shooters, and field-of-view sectors for stealth games.
2. **Enterprise Software (GIS/BIM/Digital Twin)**: Dynamic measurement lines, highlighting specific geographic polygons, and rendering radar scan ranges.
3. **Shipping Build Debugging**: Create hidden developer consoles in official Shipping builds for QA teams to visualize AI navigation, threat radii, or collision boxes in real-time.
