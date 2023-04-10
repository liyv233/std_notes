# 使用 parcel 搭建开发环境

~~~js
// 项目初始化
npm init

//安装 parcel-bundler
npm i parcel-bundler

// package.json 配置入口文件 "scripts" 替换 test
"scripts":{
"dev":"parcel src/index.html" , // src/index.html 为用户创建的入口文件
"build":"parcel build src/index.html"
}

// 设置开发依赖
npm i parcel-bundler --save-dev

// 安装 threejs 
npm i three --save

// 运行
npm run serve

// regenerationRuntimes is not defined

~~~



# 基础使用：

三个基本概念：场景、相机、渲染器。

- 场景：它是一个树状结构，由很多对象组成，可以包含多个个网格(Mesh)对象，光源(Light)对象，群组(Group)，三维物体(Object3D)，和摄像机(Camera)对象。一个场景(Scene)对象定义了场景图最基本的要素，并包了含背景色和雾等属性。这些对象通过一个层级关系明确的树状结构来展示出各自的位置和方向。子对象的位置和方向总是相对于父对象而言的。比如说汽车的轮子是汽车的子对象，这样移动和定位汽车时就会自动移动轮子。
- 相机：在three.js中，摄像机(Camera)和其他对象不同的是，它不一定要在场景图中才能起作用。相同的是，摄像机(Camera)作为其他对象的子对象，同样会继承它父对象的位置和朝向。
- 渲染器：渲染器(Renderer)，这可以说是three.js的主要对象。你传入一个场景(Scene)和一个摄像机(Camera)到渲染器(Renderer)中，然后它会将摄像机视椎体中的三维场景渲染成一个二维图片显示在画布上。



网格：几何体Geometry + 材质Material

- 网格(Mesh)对象可以理解为用一种特定的材质(Material)来绘制的一个特定的几何体(Geometry)。材质(Material)和几何体(Geometry)可以被多个网格(Mesh)对象使用。比如在不同的位置画两个蓝色立方体，我们会需要两个网格(Mesh)对象来代表每一个立方体的位置和方向。但只需一个几何体(Geometry)来存放立方体的顶点数据，和一种材质(Material)来定义立方体的颜色为蓝色就可以了。两个网格(Mesh)对象都引用了相同的几何体(Geometry)和材质(Material)。
- 几何体(Geometry)对象顾名思义代表一些几何体，如球体、立方体、平面、狗、猫、人、树、建筑等物体的顶点信息。Three.js内置了许多基本几何体 。你也可以创建自定义几何体或从文件中加载几何体。
- 材质(Material)对象代表绘制几何体的表面属性，包括使用的颜色，和光亮程度。一个材质(Material)可以引用一个或多个纹理(Texture)，这些纹理可以用来，打个比方，将图像包裹到几何体的表面。
- 

- 纹理(Texture)对象通常表示一幅要么从文件中加载，要么在画布上生成，要么由另一个场景渲染出的图像。



- 光源(Light)对象代表不同种类的光。









~~~js
import * as THREE from 'three'

// 1.构建场景对象
const scene = new THREE.Scene()

// 2.创建相机 PerspectiveCamera(角度，宽高比，近端，远端)
const camera = new THREE.PerspectiveCamera(75,window.innerHeight/window.innerWidth,0.1,1000)

// position：设置位置，（x轴，y轴，z轴）
camera.position.set(0,0,10)
scene.add(camera)

// 创建几何体对象
const cubeGeometry = new THREE.BoxGeometry() // 几何体
const cubeMaterial = new THREE.MeshBasicMaterial({color:0xffff00}) // 材质
// 根据几何体和材质创建物体
const cube = new THREE.Mesh(cubeGeometry,cubeMaterial)
// 将几何体添加到场景当中 ， 相机只会渲染场景中的物体
scene.add(cube)
// 初始化渲染器
const renderer = new THREE.WebGL1Renderer()
// 设置渲染的尺寸和大小
renderer.setSize(window.innerWidth,window.innerHeight)
// 将webgl渲染的canvas内容添加到body
document.body.appendChild(renderer.domElement)

// 使用渲染器通过相机将场景渲染进来
renderer.render(scene,camera)

~~~



## requestAnimationFrame()

**requestAnimationFrame()** 是浏览器`window`对象的方法。

`requestAnimationFrame()`**参数是将要被调用函数的函数名**，`requestAnimationFrame()`调用一个函数不是立即调用而是向浏览器发起一个执行某函数的请求， 什么时候会执行由浏览器决定，一般默认保持60FPS的频率，大约每16.7ms调用一次`requestAnimationFrame()`方法指定的函数，60FPS是理想的情况下，如果渲染的场景比较复杂或者说硬件性能有限可能会低于这个频率。





## 轨道控制器

Orbit controls（轨道控制器）可以使得相机围绕目标进行轨道运动。

要使用这一功能，就像在/examples（示例）目录中的所有文件一样， 您必须在HTML中包含这个文件。



OrbitControls( object : Camera, domElement : HTMLDOMElement )
object: （必须）将要被控制的相机。该相机不允许是其他任何对象的子级，除非该对象是场景自身。
domElement: 用于事件监听的HTML元素。



~~~js
// 导入轨道控制器
import {OrbitControls} from 'three/examples/jsm/controls/OrbitControl

//创建轨道控制器
const controls = new OrbitControls(camera,renderer.domElement)

// 渲染函数
// time指示当前被 requestAnimationFrame() 排序的回调函数被触发的时间。
function render(time) {
let t = (time / 1000) % 5;
cube.position.x = t * 1;
renderer.render(scene,camera)
// 渲染下一帧的时候就会调用 render 函数
requestAnimationFrame(render)
}

render()
~~~



- 同时使用`requestAnimationFrame()`或`controls.addEventListener('change', render)`调用同一个函数时会冲突。



## 坐标辅助器AxesHelpe

坐标辅助器简单模拟3个坐标轴的对象。红色代表 X 轴. 绿色代表 Y 轴. 蓝色代表 Z 轴。

~~~js
const axesHelper = new THREE.AxesHelper( 5 );
scene.add( axesHelper );
~~~



**ArrowHelper箭头辅助器：**

用于模拟方向的3维箭头对象

~~~js
const dir = new THREE.Vector3( 1, 2, 0 );

//normalize the direction vector (convert to vector of length 1)
dir.normalize();

const origin = new THREE.Vector3( 0, 0, 0 );
const length = 1;
const hex = 0xffff00;

const arrowHelper = new THREE.ArrowHelper( dir, origin, length, hex );
scene.add( arrowHelper )
~~~

- 构造函数：ArrowHelper(dir : Vector3, origin : Vector3, length : Number, hex : Number, headLength : Number, headWidth : Number )
  - dir -- 基于箭头原点的方向. 必须为单位向量.
  - origin -- 箭头的原点. 
  - length -- 箭头的长度. 默认为 1
  - hex -- 定义的16进制颜色值. 默认为 0xffff00.
  - headLength -- 箭头头部(锥体)的长度. 默认为箭头长度的0.2倍(0.2 * length).
  - headWidth -- 箭头头部(锥体)的宽度. 默认为箭头宽度的0.2倍 0.2 * headLength



## 移动/缩放/旋转

**物体移动：position属性**

相机和立方体都是物体。每个物体都是1个对象。

在官方文档里，我们可以看到相机camera和物体mesh都继承Object3D类。所以camera、mesh都属于3d对象。从3d对象的官方文档里，我们可以找到position属性，并且该属性一个vector3对象。

~~~js
//设置该向量的x、y 和 z 分量。
mesh.position.set(x,y,z);
//直接设置position的x,y,z属性
mesh.position.x = x;
mesh.position.y = y;
mesh.position.z = z;
~~~



**物体缩放：scale属性**

因为物体的scale属性是vector3对象，因此按照vector的属性和方法，设置x/y/z轴方向的缩放大小。

~~~js
//例如设置x轴放大3倍、y轴方向放大2倍、z轴方向不变
cube.scale.set(3, 2, 1);
//单独设置某个轴的缩放
cube.scale.x = 3
~~~



**物体选择：rotation**

因为的旋转通过设置rotation属性，该属性是Euler类的实例，因此可以通过Euler类的方法进行设置旋转角度。

~~~js
//直接设置旋转属性，例如围绕x轴旋转90度
cube.rotation.x = -Math.PI/2
//围绕x轴旋转45度
cube.rotation.set(-Math.PI / 4, 0, 0, "XZY");
~~~

- .set ( x : Float, y : Float, z : Float, order : String ) : this
- x - 用弧度表示x轴旋转量。 y - 用弧度表示y轴旋转量。 z - 用弧度表示z轴旋转量。
-  order - (optional) 表示旋转顺序的字符串。



## Clock 跟踪时间

**Clock对象：**该对象用于跟踪时间。

**实例化clock对象：**new Clock( autoStart : Boolean )

- autoStart — (可选) 是否要在第一次调用 .getDelta() 时自动开启时钟。默认值是 true。

~~~js
// 初始化时钟
const clock = new THREE.Clock();

function render() {
  // 获取时钟运行的总时长
  let time = clock.getElapsedTime();
  console.log("时钟运行总时长：", time);
  // 获取2帧之间的时间间隔
  let deltaTime = clock.getDelta ();
  console.log("2帧之间的时间间隔：", deltaTime);

  renderer.render(scene, camera);
  //   渲染下一帧的时候就会调用render函数
  requestAnimationFrame(render);
}
~~~

- .getElapsedTime ()获取自时钟启动后的秒数。
- .getDelta () 获取2帧之间的时间间隔。



## Gsap动画库

GSAP 是一个强大的 JavaScript 工具集，**构建适用于所有**主流浏览器的高性能动画。动画 CSS、SVG、画布、React、Vue、WebGL、颜色、字符串、运动路径、通用对象...... JavaScript 可以触摸的任何东西！



**安装：**npm install gsap



**创建动画：**

~~~js
// 导入动画库
import gsap from "gsap";
// 让类名为 .box 的所有元素水平移动 200px ，时间为1秒
gsap.to(".box", { x: 200 })
//立方体移动：设置1秒时间水平移动 200px
gsap.to(cube.position, { x: 200 })
~~~



**gsap属性控制动画**：

- duration：动画持续时间（秒）

- delay：动画开始前的延迟量（秒）

- repeat：动画应该重复多少次。-1为一直重复

- yoyo：如果为 true，则每隔一个重复，补间将沿相反方向运行。（像悠悠球一样）默认值：false

- ease：控制动画期间的变化率。

  - https://greensock.com/get-started/#greenSockEaseVisualizer

- onComplete：动画完成时调用的函数

- onUpdate：动画值更新时调用的函数

- onUpdateParams ：传递给onUpdate函数的参数数组。

- onStart ：动画开始时要调用的函数。

- onRepeat：当动画重复时调用的函数。需要设置repeat（大于0）才会有效。

- onRepeatParams ：传递给onRepeat 函数的参数数组。

- onReverseComplete ：当动画从反方向再次达到开始位置时要调用的函数。需要设置reversed: true

- onReverseCompleteParams ：传递给onReverseComplete函数的参数数组。

- overWrite ：如果设置true, 该对象的其他动画（任何属性）就会被停止；如果设置auto，只会停止其他动画的相关属性动画；如果false，则不停止任何动画，最后呈现的动画效果是各个动画相互作用的结果。 默认值：false

  



**补间动画：**2个关键帧（即2种物体的状态）确定，框架自带计算出中间某个时刻的状态，从而填补2个状态间，动画的空白时刻，从而实现完整动画。

**gsap.to()**： 这是最常见的补间类型。是设置**当前**元素或者变量的状态，**到设置的状态**的补间动画。

gsap.to 参数

- 参数1：目标元素或者变量：传入的是.box之类的css字符串选择器，GSAP 在后台使用document.querySelectorAll()选中页面的匹配的元素。当第一个目标是对象时，GSAP就会对其属性值进行修改来实现补间动画。

- 参数2：对应属性的目标状态，key：value

  ~~~js
  gsap.to(".box", { 
    duration: 2, //2秒
    x: 200,
    rotation: 360,
  });
  ~~~

  默认情况下，GSAP 将使用 px 和度数进行变换，也可以使用其他单位，例如 vw、弧度，甚至可以进行自己的 JS 计算或相对值！

  ~~~js
  x : 200 , // 使用 px 的默认值
  x : "+=200" // 相对值
  x : '40vw' , // 或者传入一个具有不同单位的字符串以供 GSAP 解析
  x : () => window.innerWidth / 2 , // 你甚至可以使用函数值进行计算！
  rotation：360 // 使用默认的度数
  rotation：“1.25rad” // 使用弧度
  ~~~

  如果第一个参数目标不是html元素，也可以是对象。。您可以针对任何对象的任何属性，甚至是您创建的任意属性

  ~~~js
  let obj = { myNum: 10, myColor: "red" };
  gsap.to(obj, {
    myNum: 200,
    myColor: "blue",
    onUpdate: () => console.log(obj.myNum, obj.myColor)
  });
  ~~~



**gsap.from(targets, vars)**: 产生从设置位置（或状态）到初始位置（或状态）的动画

- targets： 产生动画的对象
- vars： 目标状态参数

~~~js
gsap.from(".box", { //从100的位置移动到初始位置
  duration: 5,
  x: 100
});
~~~



**gsap.fromTo(targets, vars,vars)**: 产生从开始位置（或状态）到结束位置（或状态）的动画

- targets： 产生动画的对象
- vars（第1个）： 开始状态参数
- vars(第2个)： 结束状态参数

~~~js
gsap.fromTo(".box", { //从100的位置移动到300的位置
  x: 100
},{
  duration: 5,
  x: 300
});
~~~



**staggers 交错**：当补间有多个目标，在每个动画的开始之间添加一些交错效果

~~~js
document.querySelectorAll(".box").forEach(function(box) {
  box.addEventListener("click", function() {
    gsap.to(".box", {
      duration: 0.5, 
      opacity: 0, 
      y: -100, 
      stagger: 0.2,
      ease: "back.in"
    });
  });
});
~~~



**timelines 时间线** ：时间线是创建易于调整、有弹性的动画序列的关键。将补间添加到时间线时，默认情况下，它们会按照添加的顺序一个接一个地播放。

~~~js
// 创建时间线动画
let tl = gsap.timeline()

// 现在用tl代替以前的gsap来设置动画即可。
tl.to(".green", { x: 600, duration: 2 }); // 0-2秒期间，水平移动600px
tl.to(".purple", { x: 600, duration: 1 }); // 2-3秒期间，水平移动600px
tl.to(".orange", { x: 600, duration: 1 }); // 3-4秒期间：水平移动600px
~~~



## 画布全屏与自适应大小

**监听屏幕大小的改变**，来重新设置相机的宽高比例和渲染器的尺寸大小：

~~~js
// 监听画面变化，更新渲染画面
window.addEventListener("resize", () => {
  // 更新摄像头
  camera.aspect = window.innerWidth / window.innerHeight;
  //   更新摄像机的投影矩阵
  camera.updateProjectionMatrix();

  //   更新渲染器
  renderer.setSize(window.innerWidth, window.innerHeight);
  //   设置渲染器的像素比
  renderer.setPixelRatio(window.devicePixelRatio);
});
~~~

- aspect属性是设置摄像机视锥体的长宽比，通常是使用画布的宽/画布的高。
- camera.updateProjectionMatrix()用于更新摄像机投影矩阵，相机任何参数被改变以后必须被调用



**双击全屏与取消：**

~~~js
window.addEventListener("dblclick", () => {
  const fullScreenElement = document.fullscreenElement;
  if (!fullScreenElement) {
    //   双击控制屏幕进入全屏，退出全屏
    // 让画布对象全屏
    renderer.domElement.requestFullscreen();
  } else {
    //   退出全屏，使用document对象
    document.exitFullscreen();
  }
});
~~~

- **fullscreenElement**只读属性返回当前在此文档中以全屏模式显示的元素。如果文档当前未使用全屏模式，则返回值为null。

- **requestFullscreen()** ：元素（this)是否以全屏模式查看元素
- **exitFullscreen()**：退出全屏，使用document对象





































































































































































































































