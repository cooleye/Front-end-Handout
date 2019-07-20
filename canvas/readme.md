# Canvas
    Canvas API 提供了一个通过JavaScript 和 HTML的<canvas>元素来绘制图形的方式。
    它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。
    Canvas API主要聚焦于2D图形。而同样使用<canvas>元素的 WebGL API 则用于绘制硬件加速的2D和3D图形。

## 1. Canvas元素
canvas元素有width和height属性，默认为300*150；
对于不支持canvas标签的浏览器给予提示：
```
<canvas id="mycanvas" width="150" height="150">
        您的浏览器不支持Canvas🙂
</canvas>

<canvas id="clock" width="150" height="150">
  <img src="images/clock.png" width="150" height="150" alt=""/>
</canvas>
```
如上所示，当浏览器不支持canvas的时候，显示一段文字或者图片；

## 2. 获取渲染上下文对象
```
//获取渲染上下文对象
const canvas = document.getElementById('mycanvas');
const ctx = canvas.getContext('2d');
```
对于不支持的canvas的浏览器，也可以使用脚本给予提示：
```
 if (canvas.getContext){
    var ctx = canvas.getContext('2d');
    // 在这里开始绘画
} else {
    // 提示不支持canvas
}
```

## 3. 矩形

#### 3.1 绘制矩形的方法
* fillRect(x, y, width, height) 绘制一个填充的矩形
* strokeRect(x, y, width, height) 绘制一个矩形的边框
* clearRect(x, y, width, height) 清除指定矩形区域，让清除部分完全透明。

x与y指定了在canvas画布上所绘制的矩形的左上角（相对于原点）的坐标。width和height设置矩形的尺寸。     
==canvas 元素的默认原点为左上角。==
#### 3.2 绘制矩形
```
//设置颜色
ctx.fillStyle = "#a00";
//绘制矩形
ctx.fillRect(0,0,100,100);
```
#### 3.3 绘制有透明的矩形
```
//设置颜色和透明度
ctx.fillStyle = "rgb(0,200,0,0.5)";
ctx.fillRect(50,50,100,100);
```
#### 3.4 绘制矩形边框
```
//绘制矩形边框
ctx.strokeRect(100,0,100,100);
```
#### 3.5 清除矩形
```
//清除矩形
ctx.clearRect(100,50,100,100);
```
## 4. 绘制路径
图形的基本元素是路径。路径是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点的集合。一个路径，甚至一个子路径，都是闭合的。使用路径绘制图形需要一些额外的步骤。

1. 创建路径起始点。  
1. 使用画图命令去画出路径。    
1. 把路径封闭。  
1. 路径生成后，通过描边或填充路径区域来渲染图形。

#### 4.1 绘制路径需要用到的函数：
- beginPath() 新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
- closePath() 闭合路径之后图形绘制命令又重新指向到上下文中。
- stroke() 通过线条来绘制图形轮廓。
- fill() 通过填充路径的内容区域生成实心的图形。

#### 4.2 绘制路劲的步骤
1. 调用beginPath() 开始绘制路劲(第一次绘制，可以不用调用)
2. 调用函数指定绘制的路径
3. 闭合路径closePath()，但是必须的。这个方法会通过绘制一条从当前点到开始点的直线来闭合图形。如果图形是已经闭合了的，即当前点为开始点，该函数什么也不做。

==注意：当你调用fill()函数时，所有没有闭合的形状都会自动闭合，所以不需要调用closePath()函数。但是调用stroke()时不会自动闭合。== 

#### 4.3 绘制一个三角形
```
//绘制三角形
ctx.beginPath();
ctx.moveTo(100,0);
ctx.lineTo(150,100);
ctx.lineTo(50,100);
ctx.fill()
```
##### 4.3.1 moveTo(x, y) 移动笔触，但不会画出任何内容，可以理解为抬起笔尖，移动到你将要开始绘制的位置。
##### 4.3.2 lineTo(x, y) 绘制一条从当前位置到指定x以及y位置的直线。

绘制描边三角形：
```
// 描边三角形
ctx.beginPath();
ctx.moveTo(125,0);
ctx.lineTo(225,0);
ctx.lineTo(175,100);
//闭合路径
ctx.closePath();
//绘制描边
ctx.stroke();
```
==注意，绘制描边时（stroke），路劲不会自动闭合，需要调用closePath（）函数，否则绘制出来的只是两条线段== 

## 5. 绘制圆弧

#### arc(x, y, radius, startAngle, endAngle, anticlockwise)  
画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。
* x、y 圆心坐标
* radius 圆半径
* startAngle、endAngle 开始和结束的弧度，以x轴为基准
* anticlockwise 表示方向，布尔值。true表示顺时针，false为逆时针

==弧度=(Math.PI/180)*角度。==
圆为2π，半圆为π，90°为1/2π
```
//绘制圆弧
ctx.arc(200,200,100,0,Math.PI*2,true);
ctx.stroke();

//绘制圆形
ctx.beginPath();
ctx.arc(400,200,100,0,Math.PI*2,true);
ctx.fill()

//绘制半圆弧
ctx.beginPath();
ctx.arc(200,400,100,0,Math.PI,true);
ctx.stroke();

//绘制半圆形
ctx.beginPath();
ctx.arc(400,400,100,0,Math.PI,true);
ctx.fill()
```
## 6. 贝塞尔曲线
贝塞尔曲线分为二次贝塞尔曲线和三次贝塞尔曲线。二次贝塞尔曲线有一个开始点（蓝色）、一个结束点（蓝色）以及一个控制点（红色），而三次贝塞尔曲线有两个控制点。
![贝塞尔曲线](http://pukatu30s.bkt.clouddn.com/WechatIMG18.png)
#### 6.1 二次贝塞尔曲线
##### quadraticCurveTo(cp1x, cp1y, x, y)
绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
```
// 二次贝塞尔曲线
ctx.beginPath();
ctx.moveTo(75,25);
ctx.quadraticCurveTo(25,25,25,62.5);
ctx.quadraticCurveTo(25,100,50,100);
ctx.quadraticCurveTo(50,120,30,125);
ctx.quadraticCurveTo(60,120,65,100);
ctx.quadraticCurveTo(125,100,125,62.5);
ctx.quadraticCurveTo(125,25,75,25);
ctx.stroke();
```
#### 6.2 三次贝塞尔曲线
##### bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)
绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。

## 7. Path2D对象

Path2D()会返回一个新初始化的Path2D对象，能将某一个路径或者包含SVG path数据的字符串作为变量。

    new Path2D();     // 空的Path对象
    new Path2D(path); // 克隆Path对象
    new Path2D(d);    // 从SVG建立Path对象

Path2D.addPath(path [, transform]) 添加路径

##### 示例
```
var ctx = canvas.getContext('2d');
// 在这里开始绘画
var rectangle = new Path2D();
rectangle.rect(10, 10, 50, 50);

var circle = new Path2D();
circle.moveTo(125, 35);
circle.arc(100, 35, 25, 0, 2 * Math.PI);

ctx.stroke(rectangle);
ctx.fill(circle);
```

使用svg路径数据作为参数：
```
let p = new Path2D('M100 100 h 80 v 80 h -80 Z');
ctx.fill(p);
```
这条路径将先移动到点 (M100 100) 然后再水平移动80个单位(h 80)，然后下移80个单位 (v 80)，接着左移80个单位 (h -80)，再回到起点处 (z)。

## 8. 设置样式和颜色
* #### fillStyle = color 设置图形的填充颜色。
* #### strokeStyle = color 设置图形轮廓的颜色。
颜色的值为复合css标准的字符串，如下所示：
```
// 这些 fillStyle 的值均为 '橙色'
ctx.fillStyle = "orange";
ctx.fillStyle = "#FFA500";
ctx.fillStyle = "rgb(255,165,0)";
ctx.fillStyle = "rgba(255,165,0,1)";
```
#### 8.1 设置填充颜色
```
var ctx = canvas.getContext('2d');
for (var i=0;i<6;i++){
    for (var j=0;j<6;j++){
      ctx.fillStyle = 'rgb(' + Math.floor(255-42.5*i) + ',' + 
                       Math.floor(255-42.5*j) + ',0)';
      ctx.fillRect(j*25,i*25,25,25);
    }
}
```
#### 8.2 设置描边颜色
```
for (var i=0;i<6;i++){
    for (var j=0;j<6;j++){
        ctx.strokeStyle = 'rgb(0,' + Math.floor(255-42.5*i) + ',' + 
                        Math.floor(255-42.5*j) + ')';
        ctx.beginPath();
        ctx.arc(12.5+j*25,12.5+i*25,10,0,Math.PI*2,true);
        ctx.stroke();
    }
}
```
#### 8.3 透明度
使用 ==globalAlpha = transparencyValue== 可以设置全局透明度。不过一般用的比较少。
可以通过颜色值来设置透明度：
`rgba(255,0,0,0.5)`

#### 8.4 线型
* lineWidth = value 设置线条宽度。
* lineCap = type 设置线条末端样式。
* lineJoin = type 设定线条与线条间接合处的样式。
* getLineDash() 返回一个包含当前虚线样式，长度为非负偶数的数组。
* setLineDash(segments) 设置当前虚线样式。
* lineDashOffset = value 设置虚线样式的起始偏移量。

##### 8.4.1 lineWidth 设置线宽度
这个属性设置当前绘线的粗细。属性值必须为正数。默认值是1.0。

==线宽是指给定路径的中心到两边的粗细。换句话说就是在路径的两边各绘制线宽的一半。因为画布的坐标并不和像素直接对应，当需要获得精确的水平或垂直线的时候要特别注意。==
```
for (var i = 0; i < 10; i++){
    ctx.lineWidth = 1+i;
    ctx.beginPath();
    ctx.moveTo(5+i*14,5);
    ctx.lineTo(5+i*14,140);
    ctx.stroke();
}
```
通过上面这个示例可以发现奇数的线显示会模糊，这就是路径不能精确定位的原因。

##### 8.4.2 lineCap 线短点样式
线端点的样式的值有：
* butt 平头
* round 圆头突出
* square 平头突出

##### 8.4.3 lineJoin
lineJoin 的属性值决定了图形中两线段连接处所显示的样子。它可以是这三种之一：round, bevel 和 miter。默认是 miter。

##### 8.4.4 虚线
用 setLineDash 方法和 lineDashOffset 属性来制定虚线样式. setLineDash 方法接受一个数组，来指定线段与间隙的交替；lineDashOffset 属性设置起始偏移量.
```
var ctx = canvas.getContext('2d');
       
var offset = 0;

function draw() {
    ctx.clearRect(0,0, canvas.width, canvas.height);
    ctx.setLineDash([4, 2]);
    ctx.lineDashOffset = -offset;
    ctx.strokeRect(10,10, 100, 100);
}
// draw()
function march() {
    offset++;
    if (offset > 16) {
        offset = 0;
    }
    draw();
    setTimeout(march, 20);
}

march();
```
#### 8.5 渐变
##### 8.5.1 createLinearGradient(x1, y1, x2, y2) 线性渐变
createLinearGradient 方法接受 4 个参数，表示渐变的起点 (x1,y1) 与终点 (x2,y2)

##### gradient.addColorStop(position, color)
addColorStop 方法接受 2 个参数，position 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。例如，0.5 表示颜色会出现在正中间。color 参数必须是一个有效的 CSS 颜色值（如 #FFF， rgba(0,0,0,1)，等等）。

示例如下：
```
// 创建渐变色
var lingrad = ctx.createLinearGradient(0,0,0,150);
lingrad.addColorStop(0, '#f00');
lingrad.addColorStop(0.5, '#fff');
lingrad.addColorStop(0.5, '#fff');
lingrad.addColorStop(1, '#ff0');
// 使用渐变色
ctx.fillStyle = lingrad;
// 绘制矩形
ctx.fillRect(10,10,130,130);
```

##### 8.5.2 createRadialGradient(x1, y1, r1, x2, y2, r2)镜像渐变
createRadialGradient 方法接受 6 个参数，前三个定义一个以 (x1,y1) 为原点，半径为 r1 的圆，后三个参数则定义另一个以 (x2,y2) 为原点，半径为 r2 的圆
```
 // 创建渐变
var radgrad = ctx.createRadialGradient(45,45,10,52,50,30);
radgrad.addColorStop(0, '#A7D30C');
radgrad.addColorStop(0.9, '#019F62');
radgrad.addColorStop(1, 'rgba(1,159,98,0)');

// 画图形

ctx.fillStyle = radgrad;
ctx.fillRect(0,0,150,150);
```

## 9. 绘制文字
* fillText(text, x, y [, maxWidth])
在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的.
* strokeText(text, x, y [, maxWidth])
在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的.

文本的样式属性：
* font = value
当前我们用来绘制文本的样式. 这个字符串使用和 CSS font 属性相同的语法. 默认的字体是 10px sans-serif。
* textAlign = value
文本对齐选项. 可选的值包括：start, end, left, right or center. 默认值是 start。
* textBaseline = value
基线对齐选项. 可选的值包括：top, hanging, middle, alphabetic, * ideographic, bottom。默认值是 alphabetic。
* direction = value
文本方向。可能的值包括：ltr, rtl, inherit。默认值是 inherit。

```
ctx.font = "48px serif";
ctx.fillText("Hello world", 10, 50);
ctx.strokeText("Hello world", 10, 100);
```


## 10. 绘制图片

使用 Image() 加载图片

使用 drawImage()绘制图片。有三种情况：
#### 1、三个参数：
drawImage(image,x,y)
其中 image 是 image 或者 canvas 对象，x 和 y 是其在目标 canvas 里的起始坐标。
```
var img = new Image();   // 创建img元素
img.onload = function(){
    // 执行drawImage语句
    ctx.drawImage(img,0,0);
}
img.src = './image/dog.jpeg'; // 设置图片源地址
```
#### 2、5个参数
drawImage(image,x,y,width,height)
前三个参数还是一样的，width、height是图像显示在canvas上的大小，会缩放图像
```
ctx.drawImage(img,0,0,200,200);
```
#### 3、9个参数可以对图像进行切分
drawImage(image,sx,sy,swidth,sheight,dx,dy,dwidth,dheight)
* sx、sy表示源图像的起始位置
* swidth、sheight表示源图像的大小
* dx、dy表示图像在canvas上显示的起始位置
* dwidth、dheight表示图像在canva显示的大小
```
ctx.drawImage(img,0,0,1200,1200,0,0,100,100);
```

## 11. 状态的保存和恢复
* save()
保存画布(canvas)的所有状态
* restore()
save 和 restore 方法是用来保存和恢复 canvas 状态的，都没有参数。Canvas 的状态就是当前画面应用的所有样式和变形的一个快照。

Canvas状态存储在栈中，每当save()方法被调用后，当前的状态就被推送到栈中保存。restore()方法调用后会弹栈，返回上一个状态。
```
const canvas = document.getElementById('mycanvas');
const ctx = canvas.getContext('2d');

if (canvas.getContext){
   
    ctx.fillStyle = '#3f3'
    ctx.fillRect(0,0,150,150);   // 使用默认设置绘制一个矩形
    ctx.save();                  // 保存默认状态

    ctx.fillStyle = '#af3'       // 在原有配置基础上对颜色做改变
    ctx.fillRect(15,15,120,120); // 使用新的设置绘制一个矩形
    ctx.save();                  // 保存当前状态

    ctx.fillStyle = '#FFF'       // 再次改变颜色配置  
    ctx.fillRect(30,30,90,90);   // 使用新的配置绘制一个矩形

    ctx.restore();               // 重新加载之前的颜色状态
    ctx.fillRect(45,45,60,60);   // 使用上一次的配置绘制一个矩形

    ctx.restore();               // 加载默认颜色配置
    ctx.fillRect(60,60,30,30);   // 使用加载的配置绘制一个矩形
    
} else {
    // 提示不支持canvas
}
```


## 12. 变形
#### 12.1 移动
translate(x, y)
translate 方法接受两个参数。x 是左右偏移量，y 是上下偏移量，它用来移动 canvas 和它的原点到一个不同的位置。
```
//获取渲染上下文对象
const canvas = document.getElementById('mycanvas');
const ctx = canvas.getContext('2d');
if (canvas.getContext){
   ctx.fillStyle = "red";
   ctx.fillRect(0,0,100,100)

   ctx.translate(100,100);
   ctx.fillStyle = "blue";
   ctx.fillRect(0,0,100,100)
} else {
    // 提示不支持canvas
}
```

#### 12.2 旋转
rotate(angle)
这个方法只接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值。        
旋转的中心点始终是 canvas 的原点，如果要改变它，需要用到 translate 方法。
```
//获取渲染上下文对象
const canvas = document.getElementById('mycanvas');
const ctx = canvas.getContext('2d');
if (canvas.getContext){
    //绘制中心圆
    ctx.fillStyle = "#f00";
    ctx.translate(250,250);
    ctx.arc(0,0,20,0,Math.PI*2);
    ctx.fill()
    //绘制周围6个圆
    ctx.fillStyle = "#00a";
    for(let i = 0; i < 6; i++){
        ctx.beginPath();
        ctx.rotate(Math.PI/3);
        ctx.arc(100,0,20,0,Math.PI*2);
        ctx.fill()
    }
} else {
    // 提示不支持canvas
}
```

#### 12.3 缩放
scale  方法可以缩放画布的水平和垂直的单位。两个参数都是实数，可以为负数，x 为水平缩放因子，y 为垂直缩放因子，如果比1小，会比缩放图形， 如果比1大会放大图形。
默认值为1， 为实际大小。    
当值为负数时，可以实现镜像的效果。

```
//获取渲染上下文对象
const canvas = document.getElementById('mycanvas');
const ctx = canvas.getContext('2d');

if (canvas.getContext){
    //把画布坐标移动到画布中心
    ctx.translate(250,250)
    //保存之前的状态
    ctx.save();
    var img = new Image();   // 创建img元素
    img.onload = function(){
       /**
        * 由于onload是异步执行，会先绘制文字，
        * 这时已经放大了两倍，如果想不受文字缩放影响，
        * 可以恢复到之前保存的状态
        */
        ctx.restore()
        //-1可以实现镜像
        ctx.scale(-1,-1.5)
        ctx.drawImage(img,0,0,100,100);
    }
    img.src = './image/dog.jpeg'; // 设置图片源地址

    //缩放文字大小
    ctx.scale(2, 2);
    ctx.font = '12px serif';
    ctx.fillText('MDN', 0, 0); 
} else {
    // 提示不支持canvas
}
```


## 13. 动画
动画的基本原理是，不断的绘制新的发生变化的画面，然后不断的擦除。在一定的时间频率下重复这个动作（通常是1/25秒）擦除和绘制一次，人眼就能看到连续的画面。
#### 所以动画的步骤如下：
1. 清空 canvas
除非接下来要画的内容会完全充满 canvas （例如背景图），需要清空所有。最简单的做法就是用 clearRect 方法。
2. 保存 canvas 状态
如果你要改变一些会改变 canvas 状态的设置（样式，变形之类的），又要在每画一帧之时都是原始状态的话，你需要先保存一下。
3. 绘制动画图形（animated shapes）
这一步才是重绘动画帧。
4. 恢复 canvas 状态
如果已经保存了 canvas 的状态，可以先恢复它，然后重绘下一帧。

#### 定时重绘
* window.setInterval(function,delay)
* window.setTimeout(function,delay)
* window.requestAnimationFrame(callback)

```
    const canvas = document.getElementById('mycanvas');
    const ctx = canvas.getContext('2d');

    if (canvas.getContext){
       
        //移动原点到画布中心
        ctx.translate(250,250);
        //保存当前状态
        ctx.save();
        //设置画笔颜色
        ctx.fillStyle = "#009";
        //绘制中心蓝色大圆
        ctx.arc(0,0,50,0,Math.PI*2);
        ctx.fill()
        
        //设置转动角度
        let angle = Math.PI/180;
        function draw(){
            //清除画布
            ctx.clearRect(88,-12,24,24)
            //恢复之前的状态
            ctx.restore();
            //开启新的路径，否则会和中心圆连在一起
            ctx.beginPath()
            //设置小圆颜色
            ctx.fillStyle = "#666";
            //小圆转动角度
            ctx.rotate(angle)
            //绘制小圆
            ctx.arc(100,0,10,0,Math.PI*2);
            ctx.fill()
            //调用requestAnimationFrame，重复执行 draw函数
            requestAnimationFrame(draw)
        }

        //初次调用draw，开启动画
        draw()
    } else {
        // 提示不支持canvas
    }
```

#### 复杂的动画示例
```
 //获取渲染上下文对象
    const canvas = document.getElementById('mycanvas');
    const ctx = canvas.getContext('2d');
    //获取屏幕大小
    let screenWidth = window.screen.width;
    let screenHeight = window.screen.height;
    // console.log(screenWidth,screenHeight)
    if (canvas.getContext){
        //保存初始状态
        ctx.save();
        //设置点的个数
        const POINT_NUMBER = 100;
        //保存点坐标的数组
        let points = [];
        //移动速度
        let SPEED = 1;
        
        //初始化
        function init(){
            //清空画布
            ctx.clearRect(0,0,1280,800);
            //绘制点
            for(let i = 0;i < POINT_NUMBER;i++){
                //随机坐标
                let randX = Math.random()*screenWidth;
                let randY = Math.random()*screenHeight;
                //随机方向速率
                let xd = Math.random()*2-1;
                let yd = Math.random()*2-1;
                //保存点坐标
                points.push({x:randX,y:randY,xd:xd,yd:yd});
                ctx.beginPath()
                //绘制点
                ctx.arc(randX,randY,5,0,Math.PI*2);
                ctx.fill()
            }
        }

        init()

        function draw(){

            ctx.clearRect(0,0,1280,800);
            //绘制点
            for(let i = 0;i < POINT_NUMBER;i++){
                ctx.beginPath()
                let point = points[i];
                //根据点当前坐标和移动速率，更新点坐标
                let px = point.x + point.xd*SPEED;
                let py = point.y + point.yd*SPEED;
                //保持点在屏幕内
                if(px > 1280){
                    px = 1280;
                    point.xd*=-1;
                }
                if(px < 0){
                    px = 0;
                    point.xd*=-1;
                }
                if(py < 0){
                    py = 0;
                    point.yd*=-1;
                }
                if(py>800){
                    point.yd*=-1
                }
                point.x = px;
                point.y = py;
                //更新数组点坐标
                points[i] = point;
                //重绘点
                ctx.arc(px,py,5,0,Math.PI*2);
                ctx.fill()
            }

            /*
            * 遍历数组，使数组的点两两比较，当点距离小于200时，绘制线段
            */
            for(let i = 0; i< points.length;i++){
                //取得第一个点
                let p1 = points[i];
                for(let j = 0;j < points.length;j++){
                    //取得第二个点
                    let p2 = points[j];
                    //计算两点距离
                    let l = Math.sqrt(Math.pow(p1.x-p2.x,2) + Math.pow(p1.y-p2.y,2));
                    //当点距离小于200时，绘制线段
                    if(l < 200){
                        ctx.beginPath();
                        ctx.lineWidth = 1;
                        ctx.moveTo(p1.x,p1.y);
                        ctx.lineTo(p2.x,p2.y);
                        ctx.stroke();
                        ctx.closePath()
                    }
                }
            }
            requestAnimationFrame(draw)
        }
        //初次调用draw，开启动画
        draw()
    } else {
        // 提示不支持canvas
    }

```



## 14. canvas 转图片
#### canvas.toDataURL(type, encoderOptions);
* type： 图片格式，默认为image/png
* encoderOptions：指定图片格式为 image/jpeg 或 image/webp的情况下，可以从 0 到 1 的区间内选择图片的质量。如果超出取值范围，将会使用默认值 0.92

该方法返回一个包含 data URI 的DOMString。
#### 示例：
```
//获取渲染上下文对象
    const canvas = document.getElementById('mycanvas');
    const ctx = canvas.getContext('2d');

    if (canvas.getContext){
        //绘制一个矩形
        ctx.fillStyle = "#fff";
        ctx.fillRect(0,0,500,500);
        ctx.fillStyle = "#f00";
        ctx.font = "48px serif";
        ctx.fillText("Hello world", 200, 200);

        //点击按钮，把canvas转成图片
        document.getElementById("saveImg")
        .addEventListener("click",e =>{
            //把canvas转成图片数据
            var mediumQuality = canvas.toDataURL("image/jpeg", 0.5);
            //预览图片
            document.getElementById("preview").src = mediumQuality;
            //下载图片
            document.getElementById("down-btn").href = mediumQuality;
        })
        
    } else {
        // 提示不支持canvas
    }
```

## 15. canvas绘制图表
```
 //获取渲染上下文对象
    const canvas = document.getElementById('mycanvas');
    // const ctx = canvas.getContext('2d');

    let data = [
        {id:0,mounth:1,value:45},
        {id:1,mounth:2,value:80},
        {id:2,mounth:3,value:75},
        {id:3,mounth:4,value:150},
        {id:4,mounth:5,value:220},
        {id:5,mounth:6,value:166},
    ]

    if (canvas.getContext){
        var ctx = canvas.getContext('2d');

        ctx.save();
        ctx.fillStyle = "#fff";
        ctx.fillRect(0,0,800,500);
        //移动原点到左下角
        ctx.translate(0,500);
        //垂直镜像，笛卡尔坐标
        ctx.scale(1,-1);

        //绘制纵坐标
        ctx.moveTo(105,100);
        ctx.lineTo(105,450);
        ctx.stroke();

         //绘制横坐标线
        for(let i = 0;i < 8;i++){
            ctx.fillStyle = "#333";
            ctx.lineWidth = 0.5;
            ctx.beginPath();
            ctx.moveTo(100,100+50*i);
            ctx.lineTo(600,100+50*i);
            if(i == 0){
                ctx.lineWidth = 1;
            }
            ctx.stroke();
        }
        //绘制纵坐标
        ctx.restore()
        for(let i = 0;i< 8;i++){
            ctx.fillText(350-50*i,50,50 + 50*i)
        }

        //绘制横坐标
        ctx.beginPath();
        for(let i = 0;i < data.length;i++){
            let d = data[i];
            ctx.fillText(d.mounth+"月", 100 + 100*i, 420);
        }
        ctx.save();

        ctx.beginPath();
        //垂直镜像，笛卡尔坐标
        ctx.translate(0,500);
        ctx.scale(1,-1);
        //绘制数据点
        for(let i = 0;i < data.length;i++){
            let d = data[i];
            ctx.beginPath();
            ctx.arc(100*d.mounth,100+d.value,3,0,Math.PI*2);
            ctx.fill()
        }
        ctx.save();
       
       //绘制折线
        for(let i = 0;i < data.length;i++){
            let d = data[i];
            if(i == 0){
                ctx.beginPath();
                ctx.moveTo(100*d.mounth,100+d.value);
            }else{
                ctx.lineTo(100*d.mounth,100+d.value);
                ctx.stroke();
            }
        }
       
    } else {
        // 提示不支持canvas
    }

```