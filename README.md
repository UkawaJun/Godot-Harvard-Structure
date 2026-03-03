<img width="930" height="510" alt="e8bc1fbe3167f38bdb7b1c021a3eca13" src="https://github.com/user-attachments/assets/e8424267-2909-4f67-a13f-d4652396efc5" />***

<div align="center">

# Harvard Structure
### 3D Interactive Logic Circuit Simulator / 3D交互式逻辑电路模拟器

**开发周期 / Development Period:** 2023.11 - 2024.03

[![实机演示全景 / Gameplay Overview](https://github.com/user-attachments/assets/dacef36c-b4d3-492a-b1f3-212ce95628e8)](你的视频或项目链接)


*这是一个由我独立开发的三维逻辑电路沙盒，重点实现了逻辑节点的组件化拆分、动态 3D 连线生成以及空间交互功能。*
*This is a 3D logic circuit sandbox I developed independently, focusing on componentized node splitting, dynamic 3D wiring generation, and spatial interaction.*

</div>

---

## 💡 项目简介 / Brief Introduction

“Harvard Structure” 是我基于 Godot 引擎（GDScript）开发的一款 3D 节点式电路模拟器。为了在三维空间中自由拼装电路，我在场景中实现了一套基于两点间线性插值的动态线路生成方案。同时，我为逻辑元件设计了一套高复用率的继承结构，这使得我可以非常方便地挂载新代码，衍生出诸如“与门”、“或门”等不同功能和外观的逻辑电路板。

"Harvard Structure" is a 3D node-based circuit simulator I developed using the Godot Engine (GDScript). To assemble circuits freely in 3D space, I implemented a dynamic line generation solution based on linear interpolation between two points. Additionally, I designed a highly reusable inheritance structure for the logic components. This allows me to easily attach new scripts and derive logic circuit boards with various functions and appearances, such as "AND" and "OR" gates.

---

<div align="center">
  
### 📸 核心系统展示 / Core Systems Gallery

| 动态连线与虚线跟随 / Dynamic Wiring & Dashed Follow | 场景内3D操作轴 / In-Scene 3D Gizmos |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/525f9fba-8978-45c6-917f-50b50e756df8" width="400"> | <img src="https://github.com/user-attachments/assets/2fedd3a9-6256-4c0b-bba4-1ad3ec5a483f" width="400"> |
| **信号传递与材质反馈 / Signal & Material Feedback** | **多样化的派生节点 / Diverse Derived Nodes** |
| <img src="https://github.com/user-attachments/assets/f7b38edb-471a-40a5-b526-a3b685589e94" width="400"> | <img src="https://github.com/user-attachments/assets/6e2e3849-9f23-40be-8db8-49aeb9ad8cef" width="400"> |



</div>

---

## ⚙️ 组件化架构与二次开发 / Component Architecture & Secondary Development

为了方便后续扩展新的电路元件，我设计了一套“数据与逻辑分离”的基类模板。每一个逻辑元件在节点树中被严格拆分为四个层级：`in`（存放输入引脚，可通过编辑器直接增删改变接口数）、`out`（存放输出引脚）、`obj`（存放外观模型，如开关或灯泡）以及 `core`（挂载基础数据读取与移动交互脚本）。基于这套结果可以衍生出各种基础的门电路的功能，以及自定义的功能，用于后期包装玩家自制的集成电路的计算过程。

To facilitate the future extension of new circuit components, I designed a set of base class templates that follow the principle of "separation of data and logic." Each logic element in the node tree is strictly divided into four layers: 

  1.`in `(for input pins, where the number of interfaces can be directly added or removed via the editor), 

  2.`out `(for output pins),
  
  3.`obj `(for appearance models, such as switches or bulbs),

  4.`core `(which hosts basic data reading and movement interaction scripts). 

Based on this structure, various basic gate circuit functions can be derived, as well as custom functionalities for later packaging the computational processes of player-made integrated circuits.

### The following diagram shows the system structure based on the most basic nodes：
<div align="center">


<img src="https://github.com/user-attachments/assets/69d56312-2404-4b51-8935-a42b1dfca132" width="800" alt="核心架构流程图 / Core Architecture Flowchart">

| 场景节点层级结构 / Scene Node Hierarchy | 核心组件代码挂载 / Core Component Scripts |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/bcd408bc-c041-488b-bacb-1944f76578ca" width="380"> | <img src="https://github.com/user-attachments/assets/4885cabd-ba76-4985-b115-d88e6a96f436" width="380"> |


</div>

基于这套结构的二次开发非常便捷。当我需要制作一个新的逻辑门（例如“与非门”）时，只需复制这个基类，设定好输入输出的数量，然后在 `core` 节点下新建一个脚本写入具体的逻辑判别代码即可，完全无需重写移动和连线等底层代码。同时可以开发出很多有新意的节点，比如说灯泡就是通过二次开发的代码来反向控制材质的亮和暗来实现的。

**Secondary development based on this structure is very convenient. When I need to create a new logic gate (such as a "NAND gate"), I simply duplicate this base class, set the number of inputs and outputs, and then create a new script under the `core` node to write the specific logic evaluation code—completely without needing to rewrite underlying code for movement or connections. 
At the same time, many innovative nodes can be developed. For example, `a light bulb` is implemented through secondary development code that inversely `controls the brightness and darkness` of its material.**

---

## 🛠 开发细节与系统机制 / Development Details & System Mechanics

### 1. 动态连线与防卡顿优化 / Dynamic Wiring & Anti-Lag Optimization

在线路的生成上，程序会获取起终点坐标计算总距离，并通过线性插值生成一系列点。接着在这些点上实例化网格模型，利用反正切函数计算向量角度，使其旋转并首尾相连。为了解决拖拽电路板时实时生成完整线路导致的卡顿问题，我在移动状态下加大了插值生成的间距，使连线呈现出“虚线”的跟随效果。当松开鼠标停止移动后，程序才会重新生成高密度的完整实体线。

For line generation, the program gets the start and end coordinates, calculates the total distance, and generates a series of points via linear interpolation. Mesh models are then instantiated on these points, and arctangent functions are used to calculate vector angles, allowing them to rotate and connect end-to-end. To solve the lag caused by generating complete lines in real-time while dragging boards, I increased the interpolation spacing during movement, creating a "dashed" following effect. The program only regenerates the high-density solid line after the mouse is released.

### 2. 运行时 3D 操作轴 / Runtime 3D Gizmos

为了能在游戏运行窗口中直接操作物体，我预设了一个包含 XYZ 三向平移坐标轴和三个环形旋转轴的控件组合。当使用鼠标点击选中某个电路板时，这套坐标轴会自动吸附到该物体的坐标处。程序会读取我对操作轴的拖拽输入，并将其转化为对目标物体的空间平移和旋转操作。

To manipulate objects directly within the running game window, I preset a control widget containing XYZ translation axes and three-ring rotation axes. When a circuit board is selected with a mouse click, these coordinate axes automatically snap to the object's position. The program reads the drag inputs on the axes and translates them into spatial translation and rotation operations on the target object.

### 3. 信号距离传递与材质反馈 / Signal Distance Transmission & Material Feedback

场景内加入了一个自定义的刷新率系统来控制信号流动。电信号（能量数值）顺着连线生成的数组逐层传递，当传递距离超过信号本身的强度时，能量即发生耗尽。为了直观展示这一过程，我编写了材质替换函数，根据节点是否通电、是否被选中等状态，实时切换线路的渲染材质（如发光的高亮材质或断电的冰冻材质）。

A custom tick rate system was added to the scene to control signal flow. Electrical signals (energy values) pass sequentially along the array generated by the connections, and the energy is depleted when the transmission distance exceeds the signal's initial strength. To visually display this process, I wrote material replacement functions that switch the wire's rendering material in real-time (e.g., glowing highlight material or powered-off frozen material) based on whether the node is powered or selected.

---

## 🌱 开发心得 / Development Reflections

通过开发这个项目，我学到了非常多宝贵的工程经验。整个沙盒最初仅仅源于一个“在3D空间里连线”的简单想法，但在实际动手后，我遇到并解决了一系列关于渲染优化、空间计算和组件化架构设计的问题，一步步将其完善至今。这段经历让我深刻认识到：很多事情只要敢于去想，并朝着设定的目标不断努力追寻，最终就一定会收获一个不错的结果。

Through developing this project, I gained a wealth of valuable engineering experience. The entire sandbox initially started from a simple idea of "connecting wires in 3D space." However, during development, I encountered and resolved a series of practical issues regarding rendering optimization, spatial calculations, and componentized architecture design, perfecting it step by step to its current state. This experience made me deeply realize: as long as you dare to think and continuously pursue your set goals with effort, you will definitely reap a rewarding result in the end.
