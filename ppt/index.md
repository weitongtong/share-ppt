title: 浏览器渲染过程与原理
speaker: weitongtong
transition: slide3
files: /css/demo.css,/js/zoom.js
theme: light
usemathjax: yes

[slide]

# 浅入浅出之
# 浏览器渲染过程与原理
<small>Sharer: weitongtong</small>

[slide]

<div style="background: #fff; width: 100%; height: 100%;">
  <img src="/img/p1.png">
</div>

[slide]

## 浏览器渲染引擎
* IE（Trident）
* Chrome（Blink）
* Firefox（Gecko）
* Opera（Blink）
* Safari（Webkit）

[slide]

<div style="background: #fff; width: 100%; height: 100%;">
  <img src="/img/p2.png">
</div>
demo.html

[slide]

## DOM解析

把HTML文档解析为DOM树的过程
* 遇到`<scirpt>`标签则停止解析，先执行js {:&.rollIn}
* DOM解析完成后触发DOMContentLoaded事件， 此时图片资源并未加载完成

[slide]

## DOM Tree
<div class="dom-tree-1">
  <img src="/img/p3.png" />
  <img src="/img/p4.png" />
</div>


[slide]
## DOM Tree
DOM树结构与HTML标签一一对应

* display: none的元素也在DOM树中
* `<scirpt>`标签也在DOM树中
* 注释也在DOM树中

[slide]
## CSS解析
<div class="single-img">
  <img src="/img/p5.png" />
</div>

[slide]
## CSS解析
将CSS代码解析为CSS规则树的过程

* 与DOM解析同步进行
* 与script的执行互斥 (script会访问css或者修改css)

[slide]
## Render Tree (Render Object Tree)
<div class="single-img">
 <img src="/img/p6.png">
</div>

[slide]
## Render Tree

### DOM Tree + CSS Rules = Render Tree

* 每个节点为一个Render Object (layout Object)对象，包含宽高、位置、背景色等样式信息
* 宽高和位置是通过Layout（重排）计算出
* Render Tree和DOM Tree不完全对应
* display: none的元素不在Render Tree中
* visibility: hidden的元素在Render Tree中
* float元素、absolute元素、fixed元素会发生位置偏移
* 常说的脱离文档流，就是脱离Render Tree

[slide]
{:&.flexbox.vleft}
## 重排（Layout）/回流（reflow）
* 当修改元素的位置、大小时，引起浏览器的重排
* 对一个元素的重排，可能影响到其父级元素和相邻元素

如何避免重排？
* 用transform做形变和位移
* 通过绝对定位，脱离当前层叠上下文（即形成新的Render Layer）

[slide]
## 生成Render Layer (PaintLayers)
<div class="single-img">
 <img src="/img/p7.png">
</div>

[slide]
{:&.flexbox.vleft}
### 将Render Tree上的某些节点提升到同一个 Render Layer的过程。 一般来说，拥有相同的坐标空间的 LayoutObjects，属于同一个渲染层

* 根元素（HTML）
* 有明确的定位属性（relative、fixed、sticky、absolute）
* 透明的（opacity 小于 1)
* 有 CSS 滤镜（fliter）
* 有 CSS transform 属性（不为 none）
* overflow 不为 visible
* ...

满足以上条件的 LayoutObject 会拥有独立的渲染层，而其他的 LayoutObject 则和其第一个拥有渲染层的父元素共用一个。

demo2 demo3

[slide]
## 生成Graphics Layer
<div class="single-img">
  <img src="/img/p8.png" />
</div>

[slide]
{:&.flexbox.vleft}
### 将render Layer Tree上的某些节点进一步提升与合并
<br>
优势：
* GPU直接渲染，快于CPU
* 当需要 repaint 时，只需要 repaint 本身，不会影响到其他的层
* 对于 transform 和 opacity 效果，不会触发 layout 和 paint
demo3


[slide]
* video flash 
* iframe
* 使用 WebGL 的 canvas 元素
* 对 opacity、transform、fliter、backdropfilter 应用了 animation 或者 transition
* will-change
* ...

[slide]
* 过多的合成层会造成GPU传输的压力

[slide]
## 谢谢
