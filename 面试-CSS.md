---
typora-copy-images-to: images
---



##### 1. 说说CSS选择器以及这些选择器的优先级（有赞一面）

- `!important`
- 内联样式（1000）
- ID选择器（0100）
- 类选择器/属性选择器/伪类选择器（0010）
- 元素选择器/伪元素选择器（0001）
- 关系选择器/通配符选择器（0000）

##### 2. 你知道什么是BFC么（有赞一面）

> 小提示：这个问题重点是BFC是什么，BFC触发的条件有哪些，BFC可以干什么。这里我试着讲解了一下Boostrap的清除浮动（display：table创建匿名table-cell间接触发BFC），如果有看到别的场景使用或者自身有使用的场景可以尝试讲解一下使用技巧。这样可以让面试官觉得你不仅仅知道他问的东西是什么，你还能很好的使用它。

###### 什么是BFC

BFC 全称为块级格式化上下文 (Block Formatting Context) 。BFC是 W3C CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位以及与其他元素的关系和相互作用，当涉及到可视化布局的时候，Block Formatting Context提供了一个环境，HTML元素在这个环境中按照一定规则进行布局。一个环境中的元素不会影响到其它环境中的布局。比如浮动元素会形成BFC，浮动元素内部子元素的主要受该浮动元素影响，两个浮动元素之间是互不影响的。这里有点类似一个BFC就是一个独立的行政单位的意思。可以说BFC就是一个作用范围，把它理解成是一个独立的容器，并且这个容器里box的布局与这个容器外的box毫不相干。

###### 触发BFC的条件

- 根元素或其它包含它的元素

- 浮动元素 (元素的 `float` 不是 `none`)

- 绝对定位元素 (元素具有 `position` 为 `absolute` 或 `fixed`)

- 内联块 (元素具有 `display: inline-block`)

- 表格单元格 (元素具有 `display: table-cell`，HTML表格单元格默认属性)

- 表格标题 (元素具有 `display: table-caption`, HTML表格标题默认属性)

- 具有`overflow` 且值不是 `visible` 的块元素

- 弹性盒（`flex`或`inline-flex`）
- display: flow-root
- column-span: all

###### BFC的约束规则

- 内部的盒会在垂直方向一个接一个排列（可以看作BFC中有一个的常规流）

- 处于同一个BFC中的元素相互影响，可能会发生外边距重叠

- 每个元素的margin box的左边，与容器块border box的左边相接触(对于从左往右的格式化，否则相反)，即使存在浮动也是如此

- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然

- 计算BFC的高度时，考虑BFC所包含的所有元素，连浮动元素也参与计算

- 浮动盒区域不叠加到BFC上

###### BFC可以解决的问题

- 垂直外边距重叠问题
- 去除浮动
- 自适用两列布局（`float` + `overflow`）

##### 3. 了解盒模型么（有赞一面）

包括**内容区域**、**内边距区域**、**边框区域**和**外边距区域**。

`box-sizing: content-box`（W3C盒子模型）：元素的宽高大小表现为**内容**的大小。 `box-sizing: border-box`（IE盒子模型）：元素的宽高表现为**内容 + 内边距 + 边框**的大小。背景会延伸到边框的外沿。

IE5.x和IE6在怪异模式中使用非标准的盒子模型，这些浏览器的`width`属性不是**内容**的宽度，而是**内容**、**内边距**和**边框**的宽度的总和。

##### 4. 如何实现左侧宽度固定，右侧宽度自适应的布局（有赞一面）

> 小提示：这个问题面试官会要求说出几种解决方法。

DOM结构

```
<div class="box">
  <div class="box-left"></div>
  <div class="box-right"></div>
</div>
```

###### 利用`float + margin`实现

```
.box {
 height: 200px;
}

.box > div {
  height: 100%;
}

.box-left {
  width: 200px;
  float: left;
  background-color: blue;
}

.box-right {
  margin-left: 200px;
  background-color: red;
}
复制代码
```

###### 利用`calc`计算宽度

```
.box {
 height: 200px;
}

.box > div {
  height: 100%;
}

.box-left {
  width: 200px;
  float: left;
  background-color: blue;
}

.box-right {
  width: calc(100% - 200px);
  float: right;
  background-color: red;
}
复制代码
```

###### 利用`float + overflow`实现

```
.box {
 height: 200px;
}

.box > div {
  height: 100%;
}

.box-left {
  width: 200px;
  float: left;
  background-color: blue;
}

.box-right {
  overflow: hidden;
  background-color: red;
}
复制代码
```

###### 利用`flex`实现

这里不是最佳答案，应该是使用`flex-basis`实现更合理

```
.box {
  height: 200px;
  display: flex;
}

.box > div {
  height: 100%;
}

.box-left {
  width: 200px;
  background-color: blue;
}

.box-right {
  flex: 1; // 设置flex-grow属性为1，默认为0
  overflow: hidden;
  background-color: red;
}
```

##### 5. 如何避免回流和重绘(滴滴一面)

![reflow](./images/reflow.awebp)

- 浏览器使用流式布局模型 (Flow Based Layout)
- 浏览器会把HTML解析成DOM，把CSS解析成CSSOM，DOM和CSSOM合并就产生了Render Tree
- 有了RenderTree就能知道所有节点的样式，计算节点在页面上的大小和位置，把节点绘制到页面上
- 由于浏览器使用流式布局，对Render Tree的计算通常只需要遍历一次就可以完成，但table及其内部元素除外，通常需要多次计算且要花费3倍于同等元素的时间，这也是为什么要避免使用table布局的原因之一

###### 浏览器渲染过程

- 解析HTML，生成DOM树
- 解析CSS，生成CSSOM树
- 将DOM树和CSSOM树结合，生成渲染树(Render Tree)
- Layout(回流)：根据生成的渲染树，进行回流(Layout)，得到节点的几何信息（位置，大小）
- Painting(重绘)：根据渲染树以及回流得到的几何信息，得到节点的绝对像素
- Display：将像素发送给GPU，展示在页面上。（这一步其实还有很多内容，比如会在GPU将多个合成层合并为同一个层，并展示在页面中。而css3硬件加速的原理则是新建合成层，这里我们不展开，之后有机会会写一篇博客）

###### 何时触发回流和重绘

何时发生回流：

- 添加或删除可见的DOM元素
- 元素的位置发生变化
- 元素的尺寸发生变化（包括外边距、内边框、边框大小、高度和宽度等）
- 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代。
- 页面一开始渲染的时候（这肯定避免不了）
- 浏览器的窗口尺寸变化（因为回流是根据视口的大小来计算元素的位置和大小的）

何时发生重绘（回流一定会触发重绘）：

当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、background-color、visibility等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

有时即使仅仅回流一个单一的元素，它的父元素以及任何跟随它的元素也会产生回流。现代浏览器会对频繁的回流或重绘操作进行优化，浏览器会维护一个队列，把所有引起回流和重绘的操作放入队列中，如果队列中的任务数量或者时间间隔达到一个阈值的，浏览器就会将队列清空，进行一次批处理，这样可以把多次回流和重绘变成一次。你访问以下属性或方法时，浏览器会立刻清空队列：

- `clientWidth`、`clientHeight`、`clientTop`、`clientLeft`
- `offsetWidth`、`offsetHeight`、`offsetTop`、`offsetLeft`
- `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`
- `width`、`height`
- `getComputedStyle()`
- `getBoundingClientRect()`

以上属性和方法都需要返回最新的布局信息，因此浏览器不得不清空队列，触发回流重绘来返回正确的值。因此，我们在修改样式的时候，**最好避免使用上面列出的属性，他们都会刷新渲染队列。**如果要使用它们，最好将值缓存起来。

###### 如何避免触发回流和重绘

CSS：

- 避免使用table布局。
- 尽可能在DOM树的最末端改变class。
- 避免设置多层内联样式。
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上
- 避免使用CSS表达式（例如：`calc()`）
- CSS3硬件加速（GPU加速）

JavaScript：

- 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性
- 避免频繁操作DOM，创建一个`documentFragment`，在它上面应用所有DOM操作，最后再把它添加到文档中
- 也可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘
- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来
- 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流

##### 6. 如何实现居中(滴滴一面)

###### 水平居中

- 若是行内元素，给其父元素设置`text-align:center`即可实现行内元素水平居中
- 若是块级元素，该元素设置`margin:0 auto`即可（元素需要定宽）
- 若是块级元素，设置父元素为flex布局，子元素设置`margin:0 auto`即可（子元素不需要定宽）
- 使用flex 2012年版本布局，可以轻松的实现水平居中，子元素设置如下:

```
// flex容器
<div class="box"> 
 // flex项目
 <div class="box-center">
 </div>
</div>


.box {
  width: 200px;
  height: 200px;
  display: flex;
  // 使内部的flex项目水平居中
  justify-content: center;
  background-color: pink;
}

/* .box-center {
  width: 50%;
  background-color: greenyellow;
} */


复制代码
```

- 使用绝对定位和CSS3新增的属性`transform`（这个属性还和GPU硬件加速、固定定位相关）

```
.box {
  width: 200px;
  height: 200px;
  position: relative;
  background-color: pink;
}

.box-center {
  position: absolute;
  left:50%;
  // width: 50%;
  height: 100%;
  // 通过 translate() 方法，元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数：
  // translate(x,y)	定义 2D 转换。
  // translateX(x)	定义转换，只是用 X 轴的值。
  // translateY(y)	定义转换，只是用 Y 轴的值。
  // left: 50% 先整体向父容器的左侧偏移50%，此时是不能居中的，因为元素本身有大小
  // 接着使用transform使用百分比向左偏移本身的宽度的一半实现水平居中（这里的百分比以元素本身的宽高为基准）
  transform:translate(-50%,0);
  background-color: greenyellow;
}
复制代码
```

- 使用绝对定位和`margin-left`（元素定宽）

```
.box {
  width: 200px;
  height: 200px;
  position: relative;
  background-color: pink;
}

.box-center {
  position: absolute;
  left:50%;
  height: 100%;
  // 类似于transform
  // width: 50%;
  // margin-left: -25%;
  width: 100px;
  margin-left: -50px;
  background-color: greenyellow;
}
复制代码
```

###### 垂直居中

- 若元素是单行文本, 则可设置`line-height`等于父元素高度
- 若是块级元素，设置父元素为flex布局，子元素设置`margin: auto 0`即可（子元素不需要定宽）
- 若元素是行内块级元素，基本思想是使用`display: inline-block, vertical-align: middle`和一个伪元素让内容块处于容器中央：

```
.box {
  height: 100px;
}

.box::after, .box-center{
  display:inline-block;
  vertical-align:middle;
}
.box::after{
  content:'';
  height:100%;
}
复制代码
```

###### 居中元素高度不定

- 可用 `vertical-align` 属性（`vertical-align`只有在父层为 td 或者 th 时才会生效,，对于其他块级元素，例如 div、p 等，默认情况是不支持的），为了使用`vertical-align`，我们需要设置父元素`display:table`, 子元素 `display:table-cell;vertical-align:middle`：

```
.box {
  height: 100px;
  display: table;
}

 .box-center{
    display: table-cell;
    vertical-align:middle;
}
复制代码
```

- 可用 Flex 2012版, 这是CSS布局未来的趋势。Flexbox是CSS3新增属性，设计初衷是为了解决像垂直居中这样的常见布局问题：

```
.box {
  height: 100px;
  display: flex;
  align-items: center;
}
复制代码
```

优点：内容块的宽高任意, 优雅的溢出.  可用于更复杂高级的布局技术中.    缺点：IE8/IE9不支持、需要浏览器厂商前缀、渲染上可能会有一些问题。

- 可用 `transform` ，设置父元素相对定位：

```
.box {
  height: 100px;
  position: relative;
  background-color: pink;
}

.box-center {
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
  background-color: greenyellow;
}
复制代码
```

缺点：IE8不支持, 属性需要追加浏览器厂商前缀，可能干扰其他 `transform` 效果，某些情形下会出现文本或元素边界渲染模糊的现象。

###### 居中元素高度固定

- 设置父元素相对定位，子元素如下css样式:

```
.box {
  position:relative;
  height: 100px;
  background-color: pink;
}

.box-center{
  position:absolute;
  top:50%;
  // 注意不能使用百分比
  // margin的百分比计算是相对于父容器的width来计算的，甚至包括margin-top和margin-bottom
  height: 50px;
  margin-top: -25px;
}
复制代码
```

- 设置父元素相对定位, 子元素如下css样式:

```
.box {
  position:relative;
  width: 200px;
  height: 200px;
  background-color: pink;
}

.box-center{
  position:absolute;
  top: 0;
  bottom: 0;
  margin: auto 0;
  height: 100px;
  background-color: greenyellow;
}
复制代码
```

###### 水平垂直居中

- Flex布局（子元素是块级元素）

```
.box {
  display: flex;
  width: 100px;
  height: 100px;
  background-color: pink;
}

.box-center{
  margin: auto;
  background-color: greenyellow;
}
复制代码
```

- Flex布局

```
.box {
  display: flex;
  width: 100px;
  height: 100px;
  background-color: pink;
  justify-content: center;
  align-items: center;
}

.box-center{
  background-color: greenyellow;
}
复制代码
```

- 绝对定位实现(定位元素定宽定高)

```
.box {
  position: relative;
  height: 100px;
  width: 100px;
  background-color: pink;
}

.box-center{
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  margin: auto;
  width: 50px;
  height: 50px;
  background-color: greenyellow;
}
复制代码
```

##### 7. 用过Flex么，能简单介绍一下么(滴滴一面)

> 小提示：如果在项目中使用过，可简单介绍一下自己使用Flex解决过什么问题，这里我在项目中印象比较深刻的是使用Flex解决上面内容高度不固定，下面内容高度自动撑满父容器剩余高度的问题。

[Flex 布局教程：语法篇](https://link.juejin.cn/?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2015%2F07%2Fflex-grammar.html)

##### 8. 伪类和伪元素的区别(滴滴一面)

> 小提示：这个问题我当时懵了一下，一下子没反应过来面试官想要问什么，就答了这两者在CSS优先级上有区别，然后由于遇到不会的问题有些紧张就多说了一些废话，但显然这不是面试官想要的答案并且消耗了面试官面试的耐心，说他问的不是这个。这里再次提示大家，如果你感觉你说不清楚，但是你又知道一点，我建议你说不知道，不要纠结，面试官不会因为你不知道一个问题就PASS你，相反你说了一些无关紧要的废话，反而在消耗面试官的耐性，增加负面印象。

伪类和伪元素是用来修饰不在文档树中的部分，比如，一句话中的第一个字母，或者是列表中的第一个元素。下面分别对伪类和伪元素进行解释：

伪类用于当已有元素处于的某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的元素时，我们可以通过:hover来描述这个元素的状态。虽然它和普通的css类相似，可以为已有的元素添加样式，但是它只有处于dom树无法描述的状态下才能为元素添加样式，所以将其称为伪类。

伪元素用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过:before来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。

###### 区别

伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档树外的元素。因此，伪类与伪元素的区别在于：**有没有创建一个文档树之外的元素。**

CSS3规范中的要求使用双冒号(::)表示伪元素，以此来区分伪元素和伪类，比如::before和::after等伪元素使用双冒号(::)，:hover和:active等伪类使用单冒号(:)。除了一些低于IE8版本的浏览器外，大部分浏览器都支持伪元素的双冒号(::)表示方法。

##### 9. 绝对定位

- 一旦给元素加上`absolute`或`float`就相当于给元素加上了`display:block`
- `absolute`元素覆盖正常文档流内元素（不用设z-index，自然覆盖）
- 可以减少重绘和回流的开销（如`absolute+ top:-9999em`，或`absolute + visibility:hidden`，将动画效果放到`absolute`元素中）

###### 属性介绍

- `static`，默认值。位置设置为static的元素，它始终会处于文档流给予的位置。
- `inherit`，规定应该从父元素继承 position 属性的值。但是任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 “inherit”。
- `fixed`，生成绝对定位的元素。默认情况下，可定位于相对于浏览器窗口的指定坐标。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。不论窗口滚动与否，元素都会留在那个位置。但当祖先元素具有`transform`属性且不为none时，就会相对于祖先元素指定坐标，而不是浏览器窗口。
- `absolute`，生成绝对定位的元素，相对于距该元素最近的已定位的祖先元素进行定位。此元素的位置可通过 “left”、”top”、”right” 以及 “bottom” 属性来规定。
- `relative`，生成相对定位的元素，相对于该元素在文档中的初始位置进行定位。通过 “left”、”top”、”right” 以及 “bottom” 属性来设置此元素相对于自身位置的偏移。

浮动、绝对定位和固定定位会脱离文档流，相对定位不会脱离文档流，绝对定位相对于该元素最近的已定位的祖先元素，如果没有一个祖先元素设置定位，那么参照物是body层。

绝对定位相对于包含块的起始位置：

- 如果祖先元素是块级元素，包含块则设置为该元素的内边距边界。
- 如果祖先元素是行内元素，包含块则设置为该祖先元素的内容边界。

###### 问答

- 定位的元素的起始位置为父包含块的内边距（不会在border里，除非使用负值，会在padding里）
- 定位的元素的margin还是能起作用的
- background属性是会显示在border里的
- z-index是有层叠层级的，需要考虑同一个层叠上下文的层叠优先级
- z-index是负值不会覆盖包含块的背景色（但是如果有内容，会被包含块的内容覆盖）
- z-index的值影响的元素是定位元素以及flex盒子
- 上面一个定位元素，下面一个正常流的元素，定位元素会覆盖在正常流元素之上，除非给z-index是负值
- 页面根元素html天生具有层叠上下文，称之为“根层叠上下文”

##### 10. 说说z-index有什么需要注意的地方

> 小提示：真的忘记的差不多了，就简单说了只能在同一层叠上下文中进行`z-index`值比较、和绝对定位的关系，`z-index`值不需要设置过大，只需要理清楚层级关系即可。面试官追问了`z-index`值和`background`的覆盖关系，还追问了绝对定位元素以及后来居上的准则。面试官还问了z-index默认值是什么，`0`和`auto`有没有区别？真的对于CSS可能平常就用的不多，所以这个问题答的不是很好。

![z-index](./images/z-index.awebp)

这里附上张鑫旭的文章[深入理解CSS中的层叠上下文和层叠顺序](https://link.juejin.cn/?target=https%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2016%2F01%2Funderstand-css-stacking-context-order-z-index%2F)。

这里由于回答了定位，面试官追问固定定位的元素是相对于什么进行定位？相对定位会脱离正常文档流么？绝对定位是相对于什么元素进行定位？

##### 11. 熟悉CSS3动画么

> 小提示：CSS3动画硬件加速？CSS3动画的性能问题（重绘和重流，是否需要脱离正常文档流）？这个我当时答不知道，确实平常用的很少，如果熟悉Vue过渡动画的同学可以讲讲过渡动画？

##### 12. Flex实现两列布局

```
<div class="box">
  <div class="box-left"></div>
  <div class="box-right"></div>
</div>
复制代码
.box {
  height: 200px;
  display: flex;
}

.box > div {
  height: 100%;
}

.box-left {
  width: 200px;
  background-color: blue;
}

.box-right {
  flex: 1; // 设置flex-grow属性为1，默认为0
  overflow: hidden;
  background-color: red;
}
```

##### 13. css的引入方式，link和@import有什么区别，各自的优缺点是什么

##### 14. px,em,rem,vw,vh区别

px: px就是pixel的缩写，意为像素。px就是一张图片最小的一个点，一张位图就是千千万万的这样的点构成的。

em: **参考物是父元素**的font-size，具有继承的特点。如果自身定义了font-size按自身来计算（浏览器默认字体是16px），整个页面内1em不是一个固定的值。

rem: css3新单位，**相对于根元素html**（网页）的font-size，不会像em那样，依赖于父元素的字体大小，而造成混乱。

vw: css3新单位，viewpoint width的缩写，**视窗宽度**，1vw等于视窗宽度的1%。
 举个例子：浏览器宽度1200px, 1 vw = 1200px/100 = 12 px。

vh: css3新单位，viewpoint height的缩写，**视窗高度**，1vh等于视窗高度的1%。
 举个例子：浏览器高度900px, 1 vh = 900px/100 = 9 px。


