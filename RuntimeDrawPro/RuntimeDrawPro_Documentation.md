# RuntimeDrawPro 插件使用文档

## 1. 插件简介
**RuntimeDrawPro** 是一款专为虚幻引擎 5（Unreal Engine 5）打造的高级 C++ 插件，对蓝图极为友好。它的主要作用是**在游戏运行时（包括打包后的 Shipping 正式发行版）方便、高效地绘制各种几何图形、线条和实体**。

它彻底解决了 UE 原生 `DrawDebugLine`、`DrawDebugSphere` 等节点在打包成 Shipping 版本后自动失效的痛点。基于引擎底层的图元绘制接口（PDI）和动态网格体构建器（Dynamic Mesh Builder），本插件是玩法程序员、技术美术以及数字孪生/GIS 开发者的得力助手。

---

## 2. 安装与配置

1. **放置插件**：将 `RuntimeDrawPro` 文件夹放入您的项目根目录下的 `Plugins` 文件夹中（如果没有该文件夹请手动创建）。
2. **启用插件**：打开虚幻引擎，在顶部菜单栏选择 `Edit (编辑)` -> `Plugins (插件)`，搜索 `RuntimeDrawPro` 并勾选启用。
3. **前置依赖项**：本插件底层依赖于 UE 官方的几何体脚本系统。请在插件列表中同时搜索并启用官方的 **`Geometry Scripting`** 插件。
4. 重启引擎，等待代码编译完成即可。

---

## 3. 核心模块与功能指南

插件的所有核心功能均已暴露给蓝图，您可以直接在蓝图中搜索对应节点使用。

### 3.1 傻瓜式的生命周期管理 (URuntimeDrawBFL)
在使用本插件绘制任何图形时，**无需手动创建和销毁 Actor**。插件提供了一套全局蓝图函数库（BFL），通过 `LifeTime` 参数实现智能的垃圾回收：
* **`LifeTime = 0.0f`**：仅在当前帧绘制（单帧绘制），开销极低。
* **`LifeTime > 0.0f`**：插件会在底层自动生成一个轻量级的不可见 Actor 挂载组件，并在设定的秒数后**自动销毁**。
* **`LifeTime < 0.0f`**：永久存在。

### 3.2 绘制线框图形 (PDI Component)
支持直接在 3D 空间中渲染超过 15 种空心线框几何体。
* **支持的图形**：WireSphere (球体)、WireBox (盒体)、Rectangle (矩形)、Circle (圆形)、Arc (弧线)、WireCapsule (胶囊体)、WireCylinder (圆柱体)、WireCone (圆锥)、WireChoppedCone (圆台)、OrientedWireBox (任意朝向包围盒)、WireStar (星形坐标轴)、WireDiamond (菱形)、ConnectedArrow (连接箭头) 和 Prism (棱柱)。
* **常用参数说明**：
  * `Color`: 线条颜色。
  * `Thickness`: 线条粗细。
  * `Depth Priority`: 深度优先级（决定是否被其他物体遮挡，数值越高越容易显示在顶层）。
  * `Depth Bias`: 深度偏移（解决 Z-fighting 闪烁）。
  * `bScreenSpace`: 勾选后，线条粗细不受相机距离远近的影响，始终保持固定的屏幕像素宽度。

### 3.3 绘制实体与自定义材质 (Solid Component)
`URuntimeSolidComponent` 提供了实心几何体的渲染方案，并且**支持赋予自定义材质 (Custom Material)**，非常适合制作 AOE 技能警告区、能量护盾等效果。
* **支持的实体**：SolidTriangle (三角形)、SolidBox (盒体)、SolidSphere (球体)、SolidCone (圆锥)、SolidCylinder (圆柱体)、SolidCapsule (胶囊体)、SolidPlane (平面)、SolidTorus (圆环)、SolidDisc (圆盘) 等。
* **使用方法**：在绘制节点中，将您自己编写的材质实例（Material Instance）传入 `Material` 引脚即可。

### 3.4 高级线条与点集系统 (Line Component)
专为复杂的轨迹和标记需求打造，支持连续与离散的批量绘制。
* **Lines (连续折线)**：传入一个世界坐标数组，瞬间绘制出连贯的复杂多边形轮廓或寻路轨迹。
* **Dashed Lines (自定义虚线)**：支持设定起点和终点，并自由调节虚线的间隔长度 (`Dash Size`)。
* **Points (空间点云)**：在世界空间中批量渲染特定像素大小的标记点。

### 3.5 独家 GIS 与高级数学工具包 (GISBFL)
内置专门的 `UGISBFL` 蓝图函数库，为企业级应用和复杂游戏逻辑提供运算支持：
* **渲染目标绘制 (Render Target)**：使用 `DrawPolygonToTextureRenderTarget2D` 节点，可将任意 2D 多边形顶点坐标进行动态三角化（Triangulate），并将其直接绘制到 Render Target 上，方便小地图遮罩或动态 UI 制作。
* **几何面积与长度计算**：提供计算 2D/3D 多边形面积 (`Compute2DArea` / `Compute3DArea`) 以及折线总长度 (`ComputeLen`) 的实用节点。
* **全自动地形贴合 (Terrain Snapping)**：使用 `GetGroundPointsForPath` 或 `GenerateLineTracePointsAlongPath`，可通过自动向下发射射线，生成完美贴合起伏地形地表的路径点阵列。

---

## 4. 常见应用场景
1. **游戏玩法**：MOBA/RPG 技能范围指示器、RTS 鼠标框选框、射击游戏子弹弹道预测线、潜行游戏视野扇形区。
2. **企业级软件 (GIS/BIM/数字孪生)**：动态绘制测量线、高亮特定地理多边形区域、渲染雷达扫描半球形范围。
3. **发行版排错调试**：在 QA 团队测试的正式发行版（Shipping）中，制作隐藏开发者控制台，实时可视化 AI 寻路、仇恨半径或碰撞盒。
