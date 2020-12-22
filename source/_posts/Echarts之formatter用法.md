---
title: Echarts图表之formatter用法
date: 2018-08-24 18:39:08
tags:
---

formatter  内容格式器，把一些不够人性化的内容通过某种处理让其变得人性化，便于用户更好地理解内容，支持字符串模板和回调函数两种形式

## Echarts图表组件内的格式化常用的地方：

### 1、 legend.formatter

用来格式化图例文本。

**示例：**

```javascript
// 使用字符串模板，模板变量为图例名称 {name}
formatter: 'Legend {name}'
// 使用回调函数
formatter: function (name) {
    return 'Legend ' + name;
}
```

### 2、 xAxis.axisLabel.formatter /  yAxis.axisLabel.formatter

刻度标签的内容格式器

示例:

```javascript
// 使用字符串模板，模板变量为刻度默认标签 {value}
formatter: '{value} kg'
// 使用函数模板，函数参数分别为刻度数值（类目），刻度的索引
formatter: function (value, index) {
    // 格式化成月/日，只在第一个刻度显示年份
    var date = new Date(value);
    var texts = [(date.getMonth() + 1), date.getDate()];
    if (index === 0) {
        texts.unshift(date.getYear());
    }
    return texts.join('/');
}
```

### 3、 tooltip.formatter

提示框浮层内容格式器

 （1） **字符串模板**

模板变量有 {a}, {b}，{c}，{d}，{e}，分别表示系列名，数据名，数据值等。 在 trigger 为 'axis' 的时候，会有多个系列的数据，此时可以通过 {a0}, {a1}, {a2} 这种后面加索引的方式表示系列的索引。 不同图表类型下的 {a}，{b}，{c}，{d} 含义不一样。 其中变量{a}, {b}, {c}, {d}在不同图表类型下代表数据含义为：

折线（区域）图、柱状（条形）图、K线图 : {a}（系列名称），{b}（类目值），{c}（数值）, {d}（无）

散点图（气泡）图 : {a}（系列名称），{b}（数据名称），{c}（数值数组）, {d}（无）

地图 : {a}（系列名称），{b}（区域名称），{c}（合并数值）, {d}（无）

饼图、仪表盘、漏斗图: {a}（系列名称），{b}（数据项名称），{c}（数值）, {d}（百分比）

更多其它图表模板变量的含义可以见相应的图表的 label.formatter 配置项。

** 示例：**

```javascript
formatter: '{b0}: {c0}<br />{b1}: {c1}'
```

 (2) **回调函数**

回调函数格式：

<code>(params: Object|Array, ticket: string, callback: (ticket: string, html: string)) => string
第一个参数 params 是 formatter 需要的数据集。格式如下：</code>

```
{
    componentType: 'series',
    // 系列类型
    seriesType: string,
    // 系列在传入的 option.series 中的 index
    seriesIndex: number,
    // 系列名称
    seriesName: string,
    // 数据名，类目名
    name: string,
    // 数据在传入的 data 数组中的 index
    dataIndex: number,
    // 传入的原始数据项
    data: Object,
    // 传入的数据值
    value: number|Array,
    // 数据图形的颜色
    color: string,
    // 饼图的百分比
    percent: number,

}

```

在 trigger 为 'axis' 的时候，或者 tooltip 被 axisPointer 触发的时候，params 是多个系列的数据数组。其中每项内容格式同上，并且，

```
[{
    componentType: 'series',
    // 系列类型
    seriesType: string,
    // 系列在传入的 option.series 中的 index
    seriesIndex: number,
    // 系列名称
    seriesName: string,
    // 数据名，类目名
    name: string,
    // 数据在传入的 data 数组中的 index
    dataIndex: number,
    // 传入的原始数据项
    data: Object,
    // 传入的数据值
    value: number|Array,
    // 数据图形的颜色
    color: string,

},...
]
```

注： ECharts 2.x 使用数组表示各参数的方式不再支持。

第二个参数 ticket 是异步回调标识，配合第三个参数 callback 使用。 第三个参数 callback 是异步回调，在提示框浮层内容是异步获取的时候，可以通过 callback 传入上述的 ticket 和 html 更新提示框浮层内容。

**示例：**

```javascript
formatter: function (params, ticket, callback) {
    $.get('detail?name=' + params.name, function (content) {
        callback(ticket, toHTML(content));
    });
    return 'Loading';
}

```

### 4、 series[i]-*.label.formatter

标签内容格式器，字符串模板与回调函数返回的字符串均支持用 \n 换行。

**字符串模板** 模板变量有：

+ {a}：系列名。
+ {b}：数据名。
+ {c}：数据值。
+ {@xxx}：数据中名为'xxx'的维度的值，如{@product}表示名为'product'` 的维度的值。
+ {@[n]}：数据中维度n的值，如{@[3]}` 表示维度 3 的值，从 0 开始计数。

**示例：**

```javascript
formatter: '{b}: {@score}'
```

**回调函数** 格式：

```javascript
(params: Object|Array) => string

```

参数 params 是 formatter 需要的单个数据集。格式如下：

```
{
    componentType: 'series',
    // 系列类型
    seriesType: string,
    // 系列在传入的 option.series 中的 index
    seriesIndex: number,
    // 系列名称
    seriesName: string,
    // 数据名，类目名
    name: string,
    // 数据在传入的 data 数组中的 index
    dataIndex: number,
    // 传入的原始数据项
    data: Object,
    // 传入的数据值
    value: number|Array,
    // 数据图形的颜色
    color: string,

}
```
