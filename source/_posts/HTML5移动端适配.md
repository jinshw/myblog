---
title: HTML5移动端适配
date: 2017-11-27 09:39:22
tags: [HTML5,移动端,适配]
---

- rem 适配:

基准`html{font-size: 10px;}`
<!-- more -->
- HTML5 机型适配

| 机型 | 宽度(可视区域) | 屏幕比例 |Html font-site|元素宽度(px)|元素宽度(rem)|
| -----|:----:| ----:|----:|----:|----:|
| iphone6 plus(基准)| 414px() | 1 | 10px|200px|10rem|
|iphone6|375px|375/414 =0.9057|(375/414)*10 =9.058 px|||
|iphone5|320px|320/414 =0.7729|(320/414)*10 =7.729 px|||
|iphone4|320px|320/414 =0.7729|(320/414)*10 =7.729 px|||
|ipad&Mini|768px|768/414 =1.8551|(768/414)*10 =18.551 px||...|
|荣耀6 plus、红米2、小米2|360|360/414= 0.8695652173913043||||
|魅族4 pro|384|384/414= 0.927536231884058|||||

1. 浏览器字体最小可以设置12px，在小于12px不起作用。
2. 计算设备大小：$(window).width()

## 移动端浏览器匹配机型
方案：使用js动态计算出设备的宽度，进而计算出html中的font-size大小,其他标签使用rem单位。
1. iPhone6s、iPhone6 :测试完成 (OK)
2. 视频格式mp4
3. 双栏布局：float:left;
    - 使用box-sizing:border-box; 使border包括在盒子中。
4. `<img>`标签是否可以隐藏一部分:
使用背景图片裁切
```
<section b-id='box14344445966'
style='width:1.55rem; height:0.45rem;top:0.6rem;left:2.25rem;
position: relative;
background-position: -2.99rem -3.96rem;
background-image: url(/images/test01.jpg);
background-repeat: no-repeat;
background-size:5rem 5rem;' >
</section>
```

5. 适配方案：使用js动态计算出设备的宽度，进而计算出html中的font-size大小（依照iPhone6 plus为基准，414px）
```
var _cssText = document.querySelector("html").style.cssText;
document.querySelector("html").style.cssText  = _cssText +
"font-size:"+100*($(window).width())/414+"px !important";
```
6. 注意：测试中出现**荣耀6plus** 使用UC浏览器预览，js不能修改**html**标签的font-size 大小，
解决：设置UC浏览器字体大小为**默认**


## 微信端适配
方案：使用`@media`适配移动端，例如下面适配iPhone6 plus，其他使用rem单位

```
@media screen and (min-width: 414px) and (max-width: 767px) {
    html{
        font-size: 100px;
    }
}
```
1. 微信中左右留白15px
