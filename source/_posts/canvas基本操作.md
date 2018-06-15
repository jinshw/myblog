---
title: canvas基本操作
date: 2018-06-14 11:19:59
tags: [canvas,html5]
---
# 基础代码

html5:

~~~html
 <canvas id="canvas"></canvas>
~~~

js代码：
```javascript
var canvasHeight = 600
var canvasWidht = 800
var canvas = document.getElementById("canvas")
var context = canvas.getContext("2d")
canvas.width = canvasWidht
canvas.height = canvasHeight
```

<!--more-->

* canvas元素默认宽高300*150

* canvas元素的大小与绘图表面大小：canvas元素有两套实际上的尺寸。

  一个是元素本身大小：可以通过width和height设置（**使用这种方法可以设置canvas元素和绘图表面大小**）

  一个是元素绘图表面的大小：可以使用css设置（**不要使用这种方法**，只使用css设置宽高，如果元素大小和表面大小不一致，浏览器会放缩后者）

* `canvas.getContext("2d")` 中2d需要小写

  ​


# 直线

~~~javascript
context.save()
context.moveTo(0, 0)
context.lineTo(200, 100)
context.stroke()
context.restore()
~~~

* save 和restore : 保存和恢复当前canvas环境的所有属性；例如：需要在背景中画细不同的网格线，然后用粗线在网格上进行后续绘图。这种情况下需要临时性地修改lineWidth属性。

# 矩形

~~~javascript
context.save()
context.beginPath()
context.strokeStyle = "red"
context.rect(250, 100, 100, 80)
// context.fillRect(250, 100, 100, 80)
context.stroke()
//context.fill()
context.closePath()
context.restore()
~~~

* context.rect(x,y,width,height); 
* context.stroke() 绘制空心图形，context.fill() 填充图形

# 圆形

~~~javascript
context.save()
context.beginPath()
context.strokeStyle = "red"
context.fillStyle = "red"
context.arc(200, 100, 50, 0, Math.PI * 2)
context.stroke()
//context.fill()
context.closePath()
context.restore()
~~~

* arc(x,y,r,start,stop) 绘制圆
* context.stroke() 空心图形 ，context.fill()填充图形

# 三角形

等腰三角形

~~~javascript
var height = 200 * Math.sin(Math.PI / 3); //计算等边三角形的高
context.moveTo(100, 0); //从A（100,0）开始
context.lineTo(0, height); //从A(100,0)开始，画到B (0,173)结束
context.lineTo(200, height); //B(0,173)-C(200,173)
context.lineTo(100, 0)
context.stroke()
~~~

* 三角形就是三个坐标点连线

# 文字

空心字体

~~~javascript
ctx.font="30px Arial";
ctx.fillText("Hello World",10,50);
~~~

实心字体

~~~javascript
ctx.font="30px Arial";
ctx.strokeText("Hello World",10,50);
~~~



# 太极图

~~~javascript
function drawTaiji() {
    var x = 400
    var y = 300
    var r = 100

    var x1 = x
    var y1 = y - r / 2
    var r1 = r / 2

    var x2 = x
    var y2 = y + r / 2
    var r2 = r / 2

    var xx1 = x
    var yy1 = y - r / 2
    var rr1 = r / 8

    var xx2 = x
    var yy2 = y + r / 2
    var rr2 = r / 8

    context.save()
    context.beginPath()
    context.fillStyle = "#000"
    context.strokeStyle = "#000"
    context.arc(x, y, r, -Math.PI * 0.5, Math.PI * 0.5)
    context.fill()
    context.stroke()
    context.closePath()
    context.restore()

    context.save()
    context.beginPath()
    context.fillStyle = "#fff"
    context.strokeStyle = "#000"
    context.arc(x, y, r, -Math.PI * 0.5, Math.PI * 0.5, true)
    context.fill()
    context.stroke()
    context.closePath()
    context.restore()

    context.save()
    context.beginPath()
    context.fillStyle = "#fff"
    context.arc(x1, y1, r1, -Math.PI * 0.5, Math.PI * 0.5)
    context.fill()
    context.stroke()
    context.closePath()
    context.restore()

    context.save()
    context.beginPath()
    context.fillStyle = "#000"
    context.arc(x2, y2, r2, -Math.PI * 0.5, Math.PI * 0.5, true)
    context.fill()
    context.closePath()
    context.restore()

    context.save()
    context.beginPath()
    context.fillStyle = "#000"
    context.arc(xx1, yy1, rr1, 0, Math.PI * 2)
    context.fill()
    context.closePath()
    context.restore()

    context.save()
    context.beginPath()
    context.fillStyle = "#fff"
    context.arc(xx2, yy2, rr2, 0, Math.PI * 2)
    context.fill()
    context.closePath()
    context.restore()
}
~~~

# 以上效果图

![1529028769411](\images\canvas\st.png)



# 旋转太极图

~~~javascript
<canvas id="canvas" style="background-color: beige;border:1px solid #000;"></canvas>
<script>
        canvasWidth = 800
        canvasHeight = 600
        var canvas = document.getElementById("canvas")
        var context = canvas.getContext("2d")
        canvas.width = canvasWidth
        canvas.height = canvasHeight

        // 太极
        var x = 400
        var y = 300
        var r = 10
        temp = 2 * Math.PI / 360
        var angle = 0
        var isAdd = true
        setInterval(function () {
            context.clearRect(0, 0, canvas.width, canvas.height)
            drawTaiji(angle)
            angle = angle + 11
            if (isAdd) {
                r = r + 10
            } else {
                r = r - 10
            }


            console.log(angle)
            if (angle >= 360) {
                angle = 0
            }
            if (r >= 400) {
                //            r = 10
                isAdd = false
            } else if (r < 30) {
                isAdd = true
            }
        }, 120)
        function drawTaiji(angle) {
            var x1 = x
            var y1 = y - r / 2
            var _rotateXY1 = getRotateXY("1", angle, x, y, r) // 1:x1,y1
            if (_rotateXY1) {
                x1 = _rotateXY1.x
                y1 = _rotateXY1.y
            }
            var r1 = r / 2
            var x2 = x
            var y2 = y + r / 2
            var _rotateXY2 = getRotateXY("2", angle, x, y, r) // 2:x2,y2
            if (_rotateXY2) {
                x2 = _rotateXY2.x
                y2 = _rotateXY2.y
            }
            var r2 = r / 2

            var xx1 = x1
            var yy1 = y1
            var rr1 = r / 8
            var xx2 = x2
            var yy2 = y2
            var rr2 = r / 8
            context.clearRect(x, y, r, r)
            context.save()
            context.beginPath()
            context.fillStyle = "#000"
            context.strokeStyle = "#00"
            context.arc(x, y, r, -Math.PI * 0.5 + getAnimAngle(angle), Math.PI * 0.5 + getAnimAngle(angle))
            context.fill()
            context.closePath()
            context.stroke()
            context.restore()

            context.save()
            context.beginPath()
            context.fillStyle = "#fff"
            context.strokeStyle = "#fff"
            context.arc(x, y, r, -Math.PI * 0.5 + getAnimAngle(angle), Math.PI * 0.5 + getAnimAngle(angle), true)
            context.fill()
            context.closePath()
            context.stroke()
            context.restore()

            context.save()
            context.beginPath()
            context.fillStyle = "#fff"
            context.strokeStyle = "#fff"
            context.lineWidth = 0
            context.arc(x1, y1, r1, -Math.PI * 0.5 + getAnimAngle(angle), Math.PI * 0.5 + getAnimAngle(angle + 90))
            context.fill()
            context.closePath()
            context.restore()

            context.save()
            context.beginPath()
            context.fillStyle = "#000"
            context.strokeStyle = "#000"
            context.lineWidth = 0
            context.arc(x2, y2, r2, -Math.PI * 0.5 + getAnimAngle(angle + 90), Math.PI * 0.5 + getAnimAngle(angle), true)
            context.fill()
            context.closePath()
            context.restore()

            context.save()
            context.beginPath()
            context.fillStyle = "#000"
            context.arc(xx1, yy1, rr1, 0, Math.PI * 2)
            context.fill()
            context.closePath()
            context.restore()

            context.save()
            context.beginPath()
            context.fillStyle = "#fff"
            context.arc(xx2, yy2, rr2, 0, Math.PI * 2)
            context.fill()
            context.closePath()
            context.restore()
        }

        function getRotateXY(type, angle, x, y, r) {
            if (type == "1") {
                return getRotateXY1(angle, x, y, r)
            } else if (type == "2") {
                return getRotateXY2(angle, x, y, r)
            }
        }

        function getRotateXY2(angle, x, y, r) {
            rt = r / 2
            if (angle > 0 && angle < 90) {
                angleTemp = angle
                xt = Math.sin(angleTemp * temp) * rt
                yt = Math.cos(angleTemp * temp) * rt
                return { x: x - xt, y: y + yt }
            } else if (angle > 90 && angle < 180) {
                angleTemp = angle - 90
                xt = Math.cos(angleTemp * temp) * rt
                yt = Math.sin(angleTemp * temp) * rt
                return { x: x - xt, y: y - yt }
            } else if (angle > 180 && angle < 270) {
                angleTemp = angle - 180
                xt = Math.sin(angleTemp * temp) * rt
                yt = Math.cos(angleTemp * temp) * rt
                return { x: x + xt, y: y - yt }
            } else if (angle > 270 && angle < 360) {
                angleTemp = angle - 270
                xt = Math.cos(angleTemp * temp) * rt
                yt = Math.sin(angleTemp * temp) * rt
                return { x: x + xt, y: y + yt }
            } else {
                return null
            }

        }

        function getRotateXY1(angle, x, y, r) {
            rt = r / 2
            if (angle > 0 && angle < 90) {
                angleTemp = angle
                xt = Math.sin(angleTemp * temp) * rt
                yt = Math.cos(angleTemp * temp) * rt
                return { x: x + xt, y: y - yt }
            } else if (angle > 90 && angle < 180) {
                angleTemp = angle - 90
                xt = Math.cos(angleTemp * temp) * rt
                yt = Math.sin(angleTemp * temp) * rt
                return { x: x + xt, y: y + yt }
            } else if (angle > 180 && angle < 270) {
                angleTemp = angle - 180
                xt = Math.sin(angleTemp * temp) * rt
                yt = Math.cos(angleTemp * temp) * rt
                return { x: x - xt, y: y + yt }
            } else if (angle > 270 && angle < 360) {
                angleTemp = angle - 270
                xt = Math.cos(angleTemp * temp) * rt
                yt = Math.sin(angleTemp * temp) * rt
                return { x: x - xt, y: y - yt }
            } else {
                return null
            }

        }
        function getAnimAngle(angle) {
            return temp * angle
        }
</script>
~~~

## 效果

![](\images\canvas\taiji.gif)



# 画图板（综合事例）

## 效果

![](\images\canvas\htb.gif)

## 代码地址

https://github.com/jinshw/canvas

其中[drawboard.html](https://github.com/jinshw/canvas/blob/master/drawboard.html)


