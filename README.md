***

<div align="center">

# Harvard Structure: 3D Interactive Logic Circuit Simulator
### 哈佛结构：3D交互式逻辑电路模拟器

**开发周期 / Development Period:** 2023.11 - 2024.03

[![实机演示全景 / Gameplay Overview](https://github.com/user-attachments/assets/bfa3d733-ee33-438d-93c4-96e6ebe3995b)](你的视频或项目链接)



*这是我独立开发的一个三维逻辑电路沙盒，重点实现了节点的组件化拆分、3D连线生成以及空间交互功能。*
*This is a 3D logic circuit sandbox I developed independently, focusing on componentized node splitting, 3D wiring generation, and spatial interaction.*

</div>

---

## 💡 项目简介 / Brief Introduction

“Harvard Structure” 是我基于 Godot 引擎（GDScript）编写的 3D 节点式电路模拟器。
"Harvard Structure" is a 3D node-based circuit simulator I wrote using the Godot Engine (GDScript).

为了在三维空间中拼装电路，我没有使用引擎自带的连线工具，而是自己写了一套基于两点间线性插值的线路生成方案。
To assemble circuits in 3D space, instead of using the engine's built-in wiring tools, I wrote a line generation solution based on linear interpolation between two points.

同时，我为逻辑元件设计了一套高复用率的继承结构，方便我随时挂载新代码，开发出如“与门”、“或门”等不同功能的电路板。
Meanwhile, I designed a highly reusable inheritance structure for the logic components, allowing me to easily attach new code and develop circuit boards with different functions, such as "AND gates" and "OR gates".

---

<div align="center">
  
### 📸 核心系统展示 / Core Systems Gallery
*(2x2 Grid View)*

| 动态连线与虚线优化 / Dynamic Wiring & Dashed Optimization | 场景内3D操作轴 / In-Scene 3D Gizmos |
| :---: | :---: |
| <img src="替换为连线截图链接.png" width="400"> | <img src="替换为红蓝绿轴截图链接.png" width="400"> |
| **信号传递与材质反馈 / Signal & Material Feedback** | **组件化节点树 / Componentized Node Tree** |
| <img src="替换为发光电路截图链接.png" width="400"> | <img src="替换为节点目录截图链接.png" width="400"> |

</div>

---

## ⚙️ 组件化架构与二次开发 / Component Architecture & Secondary Development

<div align="center">
  
*(此处预留给你插入核心架构流程图的位置)*
<img src="预留你的流程图链接_替换这里.png" width="700" alt="核心架构流程图 / Core Architecture Flowchart">

</div>

为了方便后续扩展新的电路板，我设计了一套类似“数据与逻辑分离”的基类模板。
To facilitate the future expansion of new circuit boards, I designed a base class template similar to "separation of data and logic".

我将每一个逻辑元件在节点树中严格拆分为四个层级：
I strictly split each logic component into four levels within the node tree:

1. **`in` (输入) & `out` (输出)**：用于存放引脚节点，我可以通过在编辑器里增加或删除子节点，直接改变电路板的接口数量。
1. **`in` (Inputs) & `out` (Outputs)**: Used to store pin nodes. I can directly change the number of interfaces on the board by adding or removing child nodes in the editor.

2. **`obj` (物件)**：存放具体的外观模型，比如开关按钮或灯泡。
2. **`obj` (Objects)**: Stores specific appearance models, such as switch buttons or light bulbs.

3. **`core` (核心)**：挂载基础的数据读取（`basic_is`）和移动交互脚本（`NodeMove_is`）。
3. **`core` (Core)**: Mounts basic data reading (`basic_is`) and movement interaction scripts (`NodeMove_is`).

**基于这套结构的二次开发非常简单：**
**Secondary development based on this structure is very simple:**

当我需要制作一个新的“与非门”时，我只需要复制这个基类，设定好输入输出的数量，然后在 `core` 下面新建一个脚本写入与非逻辑即可，不需要重写移动和连线代码。
When I need to make a new "NAND gate", I just copy this base class, set the number of inputs and outputs, and then create a new script under `core` to write the NAND logic, without needing to rewrite the movement and wiring code.

---

## 🛠 开发细节与实际功能 / Development Details & Actual Features

### 1. 动态连线与移动防卡顿优化 / Dynamic Wiring & Anti-Lag Optimization

在线路的生成上，我会获取起点和终点的坐标，计算出总距离，然后通过线性插值生成一系列的点。
For line generation, I get the coordinates of the start and end points, calculate the total distance, and generate a series of points through linear interpolation.

接着，我会在这些点上实例化网格模型，并利用反正切函数计算向量角度，让这些模型旋转并首尾相连。
Then, I instantiate mesh models on these points and use arctangent functions to calculate vector angles, rotating these models to connect them end-to-end.

**移动优化：** 如果在拖拽电路板时实时生成完整的线，程序会非常卡顿。
**Movement Optimization:** If complete lines are generated in real-time while dragging a circuit board, the program will lag significantly.

因此我在移动状态下加大了插值生成的间距（`lds = dis/20`），此时连线只显示部分网格，呈现一种“虚线”的跟随效果。当松开鼠标停止移动后，程序才会重新生成高密度的完整实体线。
Therefore, I increased the interpolation spacing during the moving state (`lds = dis/20`), so the connection only shows partial meshes, presenting a "dashed" follow effect. The program only regenerates the complete, high-density solid line after I release the mouse and stop moving.

### 2. 运行时 3D 操作轴 / Runtime 3D Gizmos

为了能在运行的游戏窗口里操作物体，我预设了一个包含 XYZ 三向平移坐标轴和三个环形旋转轴的控件组合。
To manipulate objects within the running game window, I preset a control widget containing XYZ translation axes and three-ring rotation axes.

当使用鼠标点击选中某个电路板时，这套坐标轴会瞬间移动到该物体的坐标处。
When a circuit board is selected with a mouse click, these coordinate axes instantly move to the object's coordinates.

程序会读取我对这套操作轴的拖拽输入，并将其转化为对被选中物体的空间平移、旋转操作。
The program reads my drag inputs on these axes and translates them into spatial translation and rotation operations on the selected object.

### 3. 信号距离传递与材质反馈 / Signal Distance Transmission & Material Feedback

我加入了一个自定义的刷新率系统来控制信号的流动。
I added a custom tick rate system to control the flow of signals.

电信号（能量数值）会顺着连线生成的数组逐个传递，当传递距离超过信号本身的强度时，能量就会耗尽。
Electrical signals (energy values) pass sequentially along the array generated by the connections. When the transmission distance exceeds the signal's own strength, the energy is depleted.

为了直观地展示这个过程，我写了材质替换函数，根据节点是否通电、是否被选中，实时切换线路的材质（比如发光的绿色材质或断电的灰色材质）。
To visually display this process, I wrote material replacement functions that switch the wire's material in real-time based on whether the node is powered or selected (e.g., a glowing green material or a powered-off gray material).

---

## 🌱 开发心得 / Development Reflections

通过开发这个项目，我真的学到了很多东西。
Through developing this project, I really learned a lot.

整个工程最初只是源于一个非常简单的“在3D空间里连线”的想法。
The entire project initially started from a very simple idea of "connecting wires in 3D space."

但在实际动手后，我遇到并解决了一系列关于渲染优化、空间数学计算和组件化设计的问题，一步步将其完善。
But after actually starting, I encountered and solved a series of problems regarding rendering optimization, spatial mathematical calculations, and componentized design, optimizing it step by step.

这段经历让我深刻认识到：很多事情只要敢于去想，并朝着目标不断努力追寻，最终就一定会收获一个不错的结果。
This experience made me deeply realize: for many things, as long as you dare to think and continuously pursue the goal with effort, you will definitely reap a good result in the end.
