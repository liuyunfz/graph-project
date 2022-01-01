# graph-project

graph-project （“图”在web中的可视化）是一个基于 Vue 2.0 框架的项目，利用了 HTML5 中的 canvas 组件和 element UI。

## 实现功能

- 点的创建
- 线的创建
- DFS 输出
- BFS 输出
- Log 输出

## 截图

<details> 
![点的创建](https://cdn.jsdelivr.net/gh/liuyunfz/picgo_pictures/img/图片1.png)

![点的连通](https://cdn.jsdelivr.net/gh/liuyunfz/picgo_pictures/img/union-point.png)

![dfs](https://cdn.jsdelivr.net/gh/liuyunfz/picgo_pictures/img/dfs.png)

![bfs](https://cdn.jsdelivr.net/gh/liuyunfz/picgo_pictures/img/bfs.png) </details>



## 已知问题

由于这是一个为了应付《数据结构》课设而临时写的项目，故存在许多问题，大概率是不会再次更新优化了。已知问题如下：

1. 点的创建为矩形（可能圆形会更好
2. 点的着色会覆盖已存在的线
3. 线的连接坐标并不完全到位
4. Canvas 组件的固定大小导致不同分辨率下的显示问题
5. 由于浏览器异步渲染问题导致无法动态展示路径的访问