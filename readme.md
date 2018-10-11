## 学习D3.js
#### 1. 元素选择
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
#### 2. 绑定数据
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

**datum()**

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

**data()**

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
#### 3. 插入元素
插入元素的函数有两个：
- append(): 在选择集末尾插入元素
- insert(): 在选择集前面插入元素

**append()**
``` js
body.append('p')
  .text('在后面插入了一个元素！');
```
**insert()**

在body中id为test的元素前面添加一个段落元素
``` js
bodi.insert('p', '#test')
  .text('在id为test的元素前面插入了一个元素！');
```
#### 4. 删除元素
使用remove()方法删除元素
``` js
let p = body.select('#test');
p.remove();
```
#### 5. 制作简单图表
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
