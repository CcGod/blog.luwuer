---
title: HTML 拖拽事件执行顺序
tags: [html, draggable]
---

### 结论

1. `dragstart`	当用户开始拖动一个元素或选中的文本时触发
2. `drag`	当拖动元素或选中的文本时触发
3. `dragenter`	当拖动元素或选中的文本到一个可释放目标时触发
4. `dragover`	当元素或选中的文本被拖到一个可释放目标上时触发（每100毫秒触发一次）
5. `dragleave`	当拖动元素或选中的文本离开一个可释放目标时触发
6. `drop`	当元素或选中的文本在可释放目标上被释放时触发
7. `dragend`	当拖拽操作结束时触发 (比如松开鼠标按键或敲“Esc”键)

> 事件名为`xx`则事件处理函数为`onxx`，例如`ondragstart`

### 测试代码

> 测试代码基于[MDN文档范例](https://codepen.io/anon/pen/zMRyJB)

##### HTML
```html
<div class="dropzone">
    <div id="draggable" draggable="true" ondragstart="event.dataTransfer.setData('text/plain',null)">
        This div is draggable
    </div>
</div>
<div class="dropzone"></div> 
<div class="dropzone"></div>
<div class="dropzone"></div>
```

##### CSS
```css
  #draggable {
    width: 200px;
    height: 20px;
    text-align: center;
    background: white;
  }

  .dropzone {
    width: 200px;
    height: 20px;
    background: blueviolet;
    margin-bottom: 10px;
    padding: 10px;
  }
```

##### JAVASCRIPT
```javascript
var dragged;

var dragstart, drag, dragenter, dragover, dragleave, drop, dragend, dragexit = 0

/* 可拖动的目标元素会触发事件 */
document.addEventListener("drag", function (event) {
  if (!drag) {
    console.log('drag')
  }
  drag = 1

}, false);

document.addEventListener("dragstart", function (event) {
  if (!dragstart) {
    console.log('dragstart')
  }
  dragstart = 1
  // 保存拖动元素的引用(ref.)
  dragged = event.target;
  // 使其半透明
  event.target.style.opacity = .5;
}, false);

document.addEventListener("dragend", function (event) {
  if (!dragend) {
    console.log('dragend')
  }
  dragend = 1
  // 重置透明度
  event.target.style.opacity = "";
}, false);

/* 放下目标节点时触发事件 */
document.addEventListener("dragover", function (event) {
  if (!dragover) {
    console.log('dragover')
  }
  dragover = 1
  // 阻止默认动作
  event.preventDefault();
}, false);

document.addEventListener("dragenter", function (event) {
  if (!dragenter) {
    console.log('dragenter')
  }
  dragenter = 1
  // 当可拖动的元素进入可放置的目标高亮目标节点
  if (event.target.className == "dropzone") {
    event.target.style.background = "purple";
  }

}, false);

document.addEventListener("dragleave", function (event) {
  if (!dragleave) {
    console.log('dragleave')
  }
  dragleave = 1

  // 当拖动元素离开可放置目标节点，重置其背景
  if (event.target.className == "dropzone") {
    event.target.style.background = "";
  }

}, false);

document.addEventListener("drop", function (event) {
  if (!drop) {
    console.log('drop')
  }
  drop = 1
  // 阻止默认动作（如打开一些元素的链接）
  event.preventDefault();
  // 移动拖动的元素到所选择的放置目标节点
  if (event.target.className == "dropzone") {
    event.target.style.background = "";
    dragged.parentNode.removeChild(dragged);
    event.target.appendChild(dragged);
  }

}, false);
```

##### 输出

dragstart
drag
dragenter
dragover
dragleave
drop
dragend