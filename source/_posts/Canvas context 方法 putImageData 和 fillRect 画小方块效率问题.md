---
title: Canvas context 方法 putImageData 和 fillRect 画小方块效率问题
tags: [canvas]
date: 2017-09-19 17:40:50
---

### 实验

画12 x 12的小方块

```javascript
let canvas = document.getElementById("canvas")
let ctx = canvas.getContext("2d")

let imageData = new ImageData(12, 12)
imageData.data.forEach((item, index) => {
  imageData.data[index] = [0, 0, 0, 255][index % 4]
})
console.time()
for (let i = 0; i < 10000; i++) {
 ctx.putImageData(imageData, x + i, y + i)
}
console.timeEnd()

this.ctx.fillStyle = rgba(0, 0, 0, 155)
console.time()
for (let i = 0; i < 10000; i++) {
 ctx.fillRect(x + i, y + i, 12, 12)
}
console.timeEnd()
```
10次渲染结果：**putImageData 效率是 fillRect 的 3.3%**

| putImageData / ms | fillRect / ms | 
| -------- | ----- |
| 1.348       | 0.032     | 
| 0.767        | 0.019     | 
| 0.550       | 0.028     | 
| 0.296        | 0.020      | 

1000次渲染结果：**putImageData 效率是 fillRect 的 4.4%**

| putImageData / ms | fillRect / ms | 
| -------- | ----- |
| 40.8        | 1.99      | 
| 23.7        | 0.95      | 
| 20.0        | 0.94     | 
| 19.8        | 0.81     | 

10000次渲染结果：**putImageData 效率是 fillRect 的 21%**

| putImageData / ms | fillRect / ms | 
| -------- | ----- |
| 48        | 6      | 
| 51        | 22      | 
| 37        | 7     | 
| 55        | 7     | 

> 100000次渲染结果： **putImageData 效率是 fillRect 的 124%**


### 结论

**我们在绘制小方块时，请务必选择方法`fillRect`。**

`putImageData`有类似重复执行渲染的优化，故在大量渲染，或需要复杂彩色色块渲染时才使用它。