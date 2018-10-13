## 学习D3.js
### 1. 元素选择
html代码：
``` html
<html>
  <head>
    <title>元素选择</title>
  </head>
  <body>
    <p>hello world!!</p>
    <p>你好！！</p>
  </body>
</html>
```
javascrip代码：
``` js
// 修改内容
let p = d3.select('body')
  .selectAll('p')
  .text('这个鱼塘被我承包了！');

// 修改段落的颜色和字体大小
p.style('color', 'red')
  .style('font-size', '28px');
```
> 选择第一个元素

使用select，参数传入要选择的元素即可：
``` js
let p1 = body.select('p');
p.style('color', 'red');
```
> 选择所有元素

使用selectAll()方法选择所有要选择的元素，传入要选择的元素即可：
``` js
let p = body.selectAll('p');
p.text('hello!');
```
> 通过id选择元素，class也是一样
``` js
let p1 = body.select('#test');
```
### 2. 绑定数据
html代码：
``` html
<html>
  <head>
    <title>元素选择</title>
  </head>
  <body>
    <p>Apple</p>
    <p>Pear</p>
    <p>Banana</p>
  </body>
</html>
```
> 如何绑定数据

D3通过以下两个函数绑定数据
- datum(): 绑定一个数据到选择集上
- data(): 绑定一个数组到选择集上，数组的各项值分别与选择集各元素绑定

**1. datum()**

假设有一个字符串China,要将此字符串分别与三个段落元素绑定，代码如下：
``` js
let str = China;
let body = d3.select('body');
let p = body.selectAll('p');
p.datum(str);
p.text(function(d, i){
  return '第' + i + '个元素绑定的数据是' + d;
});
```
在上面的代码中用到了一个匿名函数`function(d, i){}`,当选择集需要使用被绑定的数据时，常需要这么做，其中包含两个参数：
- d 代表数据(data),也就是与某元素绑定的数据
- i 代表索引(index),代表数据的索引号，从0开始

**2. data()**

有一个数组，接下来要分别将数组的各元素绑定到三个段落元素上
``` js
let dataset = ['I like dogs', 'I like cats', 'I like you!'];
```
绑定之后，对应的关系应为：
- Aplle与dataset[0]绑定
- Pear与dataset[1]绑定
- Banana与dataset[2]绑定

``` js
let body = d3.select('body');
let p = body.selectAll('p');
p.data(dataset)
  .text(function(d, i){
    return d;
  });
```
### 3. 插入元素
插入元素的函数有两个：
- append(): 在选择集末尾插入元素
- insert(): 在选择集前面插入元素

**1. append()**
``` js
body.append('p')
  .text('在后面插入了一个元素！');
```
**2. insert()**

在body中id为test的元素前面添加一个段落元素
``` js
bodi.insert('p', '#test')
  .text('在id为test的元素前面插入了一个元素！');
```
### 4. 删除元素
使用remove()方法删除元素
``` js
let p = body.select('#test');
p.remove();
```
### 5. 制作简单图表
> 添加画布

推荐使用SVG做图，但是还是应该根据实际情况选择是SVG还是Canvas作画布

使用D3在body元素中添加SVG画布：
``` js
let width = 300;
let height = 300;
let svg = d3.select('body')
  .append('svg')
  .attr('height', height)
  .attr('widht', width);

  let dataset = [250, 210, 170, 130, 90]; // 数据

  let rectHeight = 25;
  svg.selectAll('rect')
  .data(dataset)
  .enter()
  .append('rect')
  .attr('x', 20)
  .attr('y', function (d, i) {
    return i * rectHeight;
  })
  .attr('width', function (d) {
    return d;
  })
  .attr('height', rectHeight - 2)
  .attr('fill', 'steelblue');
```
效果如下：

![](.png)
### 6. 比例尺的使用
前面制作了一个柱形图，当时有一个数组：
``` js
let dataset = [250, 210, 170, 130, 90]；
```
绘图的时候就是直接使用的里面的数据值作为矩形的宽度，此方法非常具有局限性，如果数值过大或者过小，例如：
``` js
let dataset_1 = [2.5, 2.1, 1.7, 1.3, 0.9];
let dataset_2 = [2500, 2100, 1700, 1300, 900];
```
对于以上两个数组，就不能直接使用数值当画布的宽度。

于是我们需要一种计算关系，能够：
***将某一区域的值映射到另一个区域，其大小关系不变！***
这就是比例尺
> 有哪些比例尺

D3的比例尺，也有定义域和值域，分别被称为domian和range。开发者需要指定domain和range的范围，由此得到一个计算关系。

D3提供了多种比例尺，下面介绍最常用的两种。

**1. 线性比例尺**

线性比例尺，能够将一个连续的区间映射到另一区间。要解决柱形图宽度的问题，就需要线性比例尺。

假设有以下数组：
``` js
let dataset = [1.2, 2.3, 0.9, 1.5, 3.3];
```
现有要求如下：

***将dataset中最小的值，映射成0；最大的值，映射成300.***

代码如下：
``` js
let min = d3.min(dataset);
let max = d3.max(dataset);
let linear = d3.scaleLinear() // D3第三版是d3.scale.linear()
  .domian([min, max])
  .rangle([0, 300]);
linear(0.9);  // ->0
linear(2.3);  // ->175
linear(3.3);  // ->300
```
**2. 序数比例尺**

有时候，定义域和值域不一定是连续的。
``` js
let index = [0, 1, 2, 3, 4];
let color = ['red', 'blue', 'green', 'yellow', 'black'];
let ordinal = d3.scaleOrinal()  // D3第三版是d3.scale.ordinal()
  .domain(index)
  .range(color);
ordinal(0); // -> red
ordinal(2); // -> green
ordinal(4); // -> black
```
> 给柱形图添加比例尺

修改数据，再定一个线性比例尺
``` js
let dataset = [2.5, 2.1, 1.7, 1.3, 0.9];
let linear = d3.scaleLinear()
  .domain([0, d3.max(dataset)])
  .range([0, 300]);
let rectHeight = 25;
svg.selectAll('rect')
  .data(dataset)
  .enter()
  .append('rect')
  .attr('x', 20)
  .attr('y', function(d, i){
    return i * rectHeight;
  })
  .attr('width', function(d){
    return linear(d); // 在这里使用比例尺
  })
  .attr('height', rectHeight - 2)
  .attr('fill', 'steelblue');
```
### 7. 坐标轴
D3提供了现成的坐标轴组件，非常简单。
> 坐标轴由什么构成

在SVG画布预定义的元素里，有六种基本图形：
- 矩形
- 圆形
- 椭圆
- 线段
- 折线
- 多边形

另外，还有一种比较特殊，也是功能最强的元素：
- **路径**

我们可以用其他元素来组合成坐标轴，最终使其变成类似于以下的形式：
``` html
<g>
  <!-- 第一个刻度 -->
  <g>
    <line></line> <!-- 第一个刻度的直线 -->
    <text></text> <!-- 第一个刻度的文字 -->
  </g>
  <!-- 第二个刻度 -->
  <g>
    <line></line> <!-- 第二个刻度的直线 -->
    <text></text> <!-- 第二个刻度的文字 -->
  </g>
  ...
  <!-- 坐标轴的轴线 -->
  <path></path>
</g>
```
但是如果手动添加这些元素就太麻烦了，D3提供了一个组件：`d3.svg.axis()`。它为我们完成了以上工作。
> 定义坐标轴

要生成坐标轴就要用到比例尺，添加一个坐标轴组件代码

**v5版本**
``` js
let linear = d3.scaleLinear()
  .domain([0, d3.max(dataset)])
  .range([0, 250]);
let axis = d3.axisBottom(linear)
  .ticks(7);
```

(v3版本)：
``` js
let axis = d3.svg.axis()
  .scale(linear)  // 指定比例尺
  .orient('bottom') // 指定刻度的位置
  .ticks(7) // 指定刻度的数量
```
代码解读：
1. `d3.svg.axis()`: D3中坐标轴的组件，能够在SVG中生成坐标轴的元素。
2. `scale()`: 指定比例尺。
3. `orient()`: 指定刻度的位置，bottom表示在坐标轴的下方。
4. `ticks()`: 指定刻度的数量。
> 在SVG中添加坐标轴

定义了坐标轴之后，只需要在SVG中添加一个分组元素，再将坐标轴的其他元素添加到这个里面即可：
``` js
svg.append('g')
  .call(axis);
```
在D3中，call()的参数是一个函数。他会将当前选择集当作参数传递给call()的形参。即：
``` js
svg.append('g').call(axis);
```
与
``` js
axis(svg.append(g));
```
是相等的。
> 设定坐标轴的样式和位置

默认的坐标轴样式不太美观，下面提供一个常见的样式：
``` html
<style>
.axis path,
.axis line {
  fill: none;
  storke: black;
  shape-rendering: crispEdges;
}

.axis text {
  font-family: sans-serif;
  font-size: 11px;
}
</style>
```
坐标轴的位置，可以通过transform属性来设定。改进后代码：
``` js
svg.append('g')
  .attr('class', 'axis')
  .attr('transform', 'translate(20, 130)')
  .call(axis);
```
效果图如下：

![](.png)

-------
### 8. 完整的柱形图
一个完整的柱形图包含三个部分：矩形、文字、坐标轴。下面代码内容包括：选择集、数据绑定、比例尺、坐标轴等内容。

> 添加SVG画布

``` js
// 画布大小
var width = 400;
var height = 400;

//在 body 里添加一个 SVG 画布
var svg = d3.select("body")
    .append("svg")
    .attr("width", width)
    .attr("height", height);

//画布周边的空白
 var padding = {left:30, right:30, top:20, bottom:20};
```
> 定义数据和比例尺

``` js
//定义一个数组
var dataset = [10, 20, 30, 40, 33, 24, 12, 5];

//x轴的比例尺
var xScale = d3.scaleBand()
    .domain(d3.range(dataset.length))
    .rangeRound([0, width - padding.left - padding.right]);

//y轴的比例尺
var yScale = d3.scaleLinear()
    .domain([0,d3.max(dataset)])
    .range([height - padding.top - padding.bottom, 0]);
```
> 定义坐标轴

``` js
// 定义x轴
let xAis = d3.axisBottom(xScale);
// 定义y轴
let yAis = d3.axisLeft(yScale);
```
> 添加矩形和文字元素

``` js
//矩形之间的空白
var rectPadding = 4;

//添加矩形元素
var rects = svg.selectAll(".MyRect")
        .data(dataset)
        .enter()
        .append("rect")
        .attr("class","MyRect")
        .attr("transform","translate(" + padding.left + "," + padding.top + ")")
        .attr("x", function(d,i){
            return xScale(i) + rectPadding/2;
        } )
        .attr("y",function(d){
            return yScale(d);
        })
        .attr("width", xScale.step() - rectPadding )
        .attr("height", function(d){
            return height - padding.top - padding.bottom - yScale(d);
        });

//添加文字元素
var texts = svg.selectAll(".MyText")
        .data(dataset)
        .enter()
        .append("text")
        .attr("class","MyText")
        .attr("transform","translate(" + padding.left + "," + padding.top + ")")
        .attr("x", function(d,i){
            return xScale(i) + rectPadding/2;
        } )
        .attr("y",function(d){
            return yScale(d);
        })
        .attr("dx",function(){
            return (xScale.step() - rectPadding)/2;
        })
        .attr("dy",function(d){
            return 20;
        })
        .text(function(d){
            return d;
        });
```
> 添加坐标轴的元素

``` js
//添加x轴
svg.append("g")
  .attr("class","axis")
  .attr("transform","translate(" + padding.left + "," + (height - padding.bottom) + ")")
  .call(xAxis);

//添加y轴
svg.append("g")
  .attr("class","axis")
  .attr("transform","translate(" + padding.left + "," + padding.top + ")")
  .call(yAxis);
```
坐标轴的位置要结合空白padding的值来设定。
### 9. 让图表动起来
> 实现动态的方法
D3提供了4个方法实现图形的过渡：从状态A变成状态B。

**1. transition()**

颜色变化：
``` js
.attr('fill', 'red')  // 初始颜色
.transition()         // 启动过渡
.attr('fill', 'steelblue')  // 最终颜色
```
**2. duration()**

指定过渡的持续时间，单位为毫秒。
如`duration(2000)`。

**3. ease()**

过渡方式：

- easeLinear: 普通的线性变化
- easeCircle: 慢慢地到达变换的最终状态
- easeElastic: 带有弹跳的到达最终的状态
- easeBounce: 在最终状态处弹跳几次

调用形式如：`ease(d3.easeBounce)`。

**4. delay()**

指定延迟的时间，表示一定事件之后才开始转变，单位为毫秒。此函数可以对整体指定延迟，也可以对个别指定延迟。

例一、对整体指定：
``` js
.transition()
.duration(1000)
.delay(500)
```
例二、对一个一个的图形（图形上绑定了数据）进行指定时：
``` js
.transition()
.duration(1000)
.delay(function (d, i) {
  return 200 * i;
})
```
> 实现简单的动态效果

下面将在SVG画布里面添加三个圆，圆出现之后，立即出现过度效果。

第一个圆，要求移动x坐标。
``` js
let circle = svg.append('circle')
    .attr('cx', 100)
    .attr('cy', 100)
    .attr('r', 45)
    .style('fill', 'green');
// 在一秒内将圆心坐标轴由100变为300
circle.transtion()
  .duration(1000)
  .attr('cr', 300);
```
第二个圆，要求即移动X坐标，又改变颜色。
``` js
circle.transition()
  .duration(1500)
  .attr('cx', 300)
  .style('fill', 'red');
```
第三个圆，要求即移动x坐标，又改变颜色，还改变半径。
``` js
circle.transtion()
  .duration(2000)
  .ease('bounce')
  .attr('cx', 300)
  .style('fill', 'red')
  .attr('r', 25);
```
> 给柱形图加上动态效果

对前面完整柱形图的基础上稍作修改，既可作成一个代动态效果的，有意思的柱形图。在添加文字元素和矩形元素的时候，启动过渡效果，让各柱形和文字缓慢升至目标高度，并且在目标处跳动几次。

对于文字元素，代码如下：
``` js
.attr('y', function (d) {
  let min = yScale.domain()[0];
  return yScale(min);
})
.transition()
.delay(function(d, i){
  return i * 200;
})
.duration(2000)
.ease('bounce')
.attr('y', function(d){
  return yScale(d);
})
```
