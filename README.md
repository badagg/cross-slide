# cross-slide 滑动Tabs

> 类似于抖音界面的滑动框架 支持左右滑动切类目 上下滑动切卡片

## 特点
1. 同时支持鼠标设备和触摸设备
2. 优化过竖向滑动的列表（始终保持有限个数）

## 用法
调用<CorssSlide />组件，传入对应config（如）：
```js
[
  { name: 'tab1', selected: true, index: 0, list: [0,1,2,3,4,5,6,7,8,9] },
  { name: 'tab2', selected: false, index: 10, list: ['a','b','c','d','e','f','g','h','i','j','k'] },
  { name: 'tab3', selected: false, index: 0, list: ['一','二','三','四'] },
]
```

## 事件方法

`@horizontal-prev` 横向滑动上一页 参数：`index`

`@horizontal-next` 横向滑动下一页 参数：`index`

`@vertical-prev` 竖向滑动上一页 参数：`{ horizontalIndex, verticalIndex }`

`@vertical-next` 竖向滑动下一页 参数：`{ horizontalIndex, verticalIndex }`