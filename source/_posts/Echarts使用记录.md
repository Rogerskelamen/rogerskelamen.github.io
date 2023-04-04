---
title: Echarts使用记录
date: 2022-04-08 16:43:03
index_img: https://s2.loli.net/2022/04/08/ByFEo8i3DVXfkHY.png
author: Rogers Kelamen
tags:
- js
categories:
- 前端
---


# Echarts使用注意点

> 一般来说，echarts的难点都是在配置(`option`)上。在这里也主要讲解配置

---

几个常用的配置项

- `title`

  图表的标题

- `legend`

  图例

- `grid`

  拥有直角坐标系的图表中的网格

- `xAxis`

  X坐标轴

  *注意，坐标轴配置中包含了坐标轴文本，坐标轴单位，坐标轴个数，坐标轴刻度的配置*

  - `type`

    坐标轴的类型: `value`, `category`, `time`, `log`

  - `axisLine`

    坐标轴线的设置

  - `axisTick`

    坐标轴刻度的设置

  - `axisLabel`

    坐标轴刻度标签的相关设置。

  - `data`

    坐标轴上的标签数据(*适用于4.0版本以前*)

- `yAxis`

  Y坐标轴

- `dataZoom`

  用于拥有大量数据图表的区域缩放，分为坐标系中和坐标系外

- `tooltip`

  悬浮于图表图形上的提示框组件

- `toolbox`

  工具栏,内置有导出图片，数据视图，动态类型切换，数据区域缩放，重置五个工具。

- `dataset`

  统一管理图表中的数据集配置项

- `series`

  最为重要的配置项，这里面就是实际图表图形的配置，主要有图表类型的配置

- `color`

  图表调色板的配置(显示那些颜色)

- `textStyle`

  全局字体的样式

---

图形的类型是写在`series`中的

```js
option = {
  xAxis: {
    type: 'category',
    data: ['Matcha Latte', 'Milk Tea', 'Cheese Cocoa', 'Walnut Brownie']
  },
  yAxis: {},
  series: [
    {
      type: 'bar',
      name: '2015',
      data: [89.3, 92.1, 94.4, 85.4]
    },
    {
      type: 'bar',
      name: '2016',
      data: [95.8, 89.4, 91.2, 76.9]
    },
    {
      type: 'bar',
      name: '2017',
      data: [97.7, 83.1, 92.5, 78.1]
    }
  ]
}
// 来自echarts官方文档
```

*ps: 现在建议将data放入到`dataset`这个对象属性中*

```js
option = {
  legend: {},
  tooltip: {},
  dataset: {
    // 提供一份数据。
    source: [
      ['product', '2015', '2016', '2017'],
      ['Matcha Latte', 43.3, 85.8, 93.7],
      ['Milk Tea', 83.1, 73.4, 55.1],
      ['Cheese Cocoa', 86.4, 65.2, 82.5],
      ['Walnut Brownie', 72.4, 53.9, 39.1]
    ]
  },
  // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
  xAxis: { type: 'category' },
  // 声明一个 Y 轴，数值轴。
  yAxis: {},
  // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
};
```

---

有时候我们传过来的数据可能不满足我们的echarts图表默认规则，这时候我们就需要进行数据映射了(`encode`):

```js
option = {
  series: {
    // 注意维度序号（dimensionIndex）从 0 开始计数，第三列是 dimensions[2]。
    encode: { x: 2, y: 4 }
    // ...
  }
};

// 把第三列设置为 X 轴，第五列设置为 Y 轴
```

---

听说过echarts的数据转换吗？这种方式可以简单的将原数据集转换变成我们想要的数据集(**当然这个方式需要当前图表支持`dataset`方式设置数据**)

看看简单的用法：

```js
var option = {
  dataset: [
    {
      // 这个 dataset 的 index 是 `0`。
      source: [
        ['Product', 'Sales', 'Price', 'Year'],
        ['Cake', 123, 32, 2011],
        ['Cereal', 231, 14, 2011],
        ['Tofu', 235, 5, 2011],
        ['Dumpling', 341, 25, 2011],
        ['Biscuit', 122, 29, 2011],
        ['Cake', 143, 30, 2012],
        ['Cereal', 201, 19, 2012],
        ['Tofu', 255, 7, 2012],
        ['Dumpling', 241, 27, 2012],
        ['Biscuit', 102, 34, 2012],
        ['Cake', 153, 28, 2013],
        ['Cereal', 181, 21, 2013],
        ['Tofu', 395, 4, 2013],
        ['Dumpling', 281, 31, 2013],
        ['Biscuit', 92, 39, 2013],
        ['Cake', 223, 29, 2014],
        ['Cereal', 211, 17, 2014],
        ['Tofu', 345, 3, 2014],
        ['Dumpling', 211, 35, 2014],
        ['Biscuit', 72, 24, 2014]
      ]
      // id: 'a'
    },
    {
      // 这个 dataset 的 index 是 `1`。
      // 这个 `transform` 配置，表示，此 dataset 的数据，来自于此 transform 的结果。
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2011 }
      }
      // 我们还可以设置这些可选的属性： `fromDatasetIndex` 或 `fromDatasetId`。
      // 这些属性，指定了，transform 的输入，来自于哪个 dataset。例如，
      // `fromDatasetIndex: 0` 表示输入来自于 index 为 `0` 的 dataset 。又例如，
      // `fromDatasetId: 'a'` 表示输入来自于 `id: 'a'` 的 dataset。
      // 当这些属性都不指定时，默认认为，输入来自于 index 为 `0` 的 dataset 。
    },
    {
      // 这个 dataset 的 index 是 `2`。
      // 同样，这里因为 `fromDatasetIndex` 和 `fromDatasetId` 都没有被指定，
      // 那么输入默认来自于 index 为 `0` 的 dataset 。
      transform: {
        // 这个类型为 "filter" 的 transform 能够遍历并筛选出满足条件的数据项。
        type: 'filter',
        // 每个 transform 如果需要有配置参数的话，都须配置在 `config` 里。
        // 在这个 "filter" transform 中，`config` 用于指定筛选条件。
        // 下面这个筛选条件是：选出维度（ dimension ）'Year' 中值为 2012 的所有
        // 数据项。
        config: { dimension: 'Year', value: 2012 }
      }
    },
    {
      // 这个 dataset 的 index 是 `3`。
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2013 }
      }
    }
  ],
  series: [
    {
      type: 'pie',
      radius: 50,
      center: ['25%', '50%'],
      // 这个饼图系列，引用了 index 为 `1` 的 dataset 。也就是，引用了上述
      // 2011 年那个 "filter" transform 的结果。
      datasetIndex: 1
    },
    {
      type: 'pie',
      radius: 50,
      center: ['50%', '50%'],
      datasetIndex: 2
    },
    {
      type: 'pie',
      radius: 50,
      center: ['75%', '50%'],
      datasetIndex: 3
    }
  ]
};
```

