---
title: echarts使用笔记
date: 2018-07-13 10:31:30
tags:
---

## 一、在webpack中使用ECharts

### npm 安装 ECharts

 使用下面的命令通过npm安装ECharts
  `npm install echarts --save`

### 引入ECharts

 1. 可以直接在项目代码中 `require('echarts')`来整体引入ECharts。

 ```javascript
 var echarts = require('echarts');
 // 基于准备好的dom，初始化echarts实例
 var myChart = echarts.init(document.getElementById('main'));

 // 绘制图表
 myChart.setOption({
   tooltip: { // 提示框
    trigger: 'axis'
   },
   legend: {
     top: 90,
     right: 8,
     itemWidth: 16,
     itemHeight: 12,
     orient: 'vertical',
     data: ['小学', '初中', '高中']
   },
   toolbox: { // 工具箱
     right: 16,
     top: 0,
     itemGap: 16, // 工具栏 icon 每项之间的间隔。横向布局时为水平间隔，纵向布局时为纵向间隔。
     feature: { // 各个工具配置项
       myTool1: {
         show: true,
         title: '导出表格',
         icon: 'image://../../../../static/images/Excel-download@2x.png',
         onclick: () => {
           downloadExl(this.exportData, '', this.elementId)
         }
       },
       saveAsImage: {
         title: '导出图片',
         type: 'png',
         excludeComponents: [ // 保存图表时忽略的工具组件,默认忽略工具栏
           'toolbox'
         ],
         icon: 'image://../../../../static/images/Pic-download@2x.png'
       }
     }
   },
   xAxis: {
     data: this.subList,
     axisLine: {show: false}, // 是否显示坐标轴轴线。
     nameTextStyle: {color: '#212121'},
     axisTick: {show: false} // 是否显示坐标轴刻度。
   },
   yAxis: {
     axisLine: {show: false},
     axisTick: {show: false},
     splitLine: { // 坐标轴在 grid 区域中的分隔线
       lineStyle: {type: 'dashed'} // 分隔线线的类型。
     }
   },
   series: [
     {
       name: '小学',
       type: 'bar',
       barWidth: 16,
       barGap: '80%',
       data: this.xiaoCnt,
       label: {
         normal: {
           show: true,
           position: 'top',
           rotate: 45
         }
       },
       itemStyle: {
         normal: {
           color: '#FFE066',
           label: {
             show: true,
             textStyle: {
               color: '#000'
             }
           }
         }
       }
     },
     {
       name: '初中',
       type: 'bar',
       barWidth: 16,
       barGap: '80%',
       data: this.chuCnt,
       label: {
         normal: {
           show: true,
           position: 'top',
           rotate: 45
         }
       },
       itemStyle: {
         normal: {
           color: '#8CE99A',
           label: {
             show: true,
             textStyle: {
               color: '#000'
             }
           }
         }
       }
     },
     {
       name: '高中',
       type: 'bar',
       barWidth: 16,
       barGap: '80%',
       data: this.gaoCnt,
       label: {
         normal: {
           show: true,
           position: 'top',
           rotate: 45
         }
       },
       itemStyle: {
         normal: {
           color: '#72C3FC',
           label: {
             show: true,
             textStyle: {
               color: '#000'
             }
           }
         }
       }
     }
   ],
   // 控制边距
   grid: {
     left: 0,
     right: '8%',
     top: 30,
     bottom: 0,
     containLabel: true
   }
 })
 ```

 2. 按需引入ECharts图表和组件

 默认使用`require('echarts')`得到的是已经加载了所有图表和组件的ECharts包，在项目中，可以只按需引入需要的模块。

 例如上面实例代码中只用到了柱状图、title、提示框、工具组件、图例组件，因此在引入的时候也只需要引入这些模块。

```javascript
// 引入ECharts 主模块
var echarts = require('echarts/lib/echarts')
require('echarts/lib/chart/bar') // 引入柱状图
require('echarts/lib/component/tooltip') // 信息提示框组件
require('echarts/lib/component/toolbox') // 引入工具组件
require('echarts/lib/component/legend') // 引入图例组件
require('echarts/lib/component/title') // title组件

```

可以按需引入的模块列表见 (https://github.com/ecomfe/echarts/blob/master/index.js)

## 二、图表的样式

Echarts 提供了丰富的自定义配置选项，并且能够从全局、系列、数据3个层级去设置数据图形的样式。

使用Echarts实现下面这个南丁格尔图：

<img src="images/1531462727629.jpg" width="500" title="南丁格尔图"/>

### 绘制南丁格尔图

这次要画的是饼图，饼图主要是通过扇形的弧度表现不同类目的数据在总和中的占比，它的数据格式比柱状图更简单，只有一维的数值，不需要给类目。因为不在直角坐标系上，所以也不需要`xAxis`, `yAxis`。

```javascript
myChart.setOption({
    series : [
        {
            name: '入库试题量',
            type: 'pie',
            radius: [0, '60%'],
            center: ['50%', '50%'],
            data:[
                {value: 7307, name: '录排订单'},
                {value: 948, name: '改编订单'},
                {value: 4064, name: 'Word订单'},
                {value: 2711, name: '直接录入'}
            ]
        }
    ]
})
```
上面的代码就能画出一个简单的饼图：

<img src="images/1531463962589.jpg" width="300" title="饼图"/>

这里的data属性不像介绍ECharts引入方式时柱状图里那样每一项都是单个数值，而是一个包含 `name` 和 `value`属性的对象，ECharts中的数据项都是既可以只设成数值，也可以设成一个包含有名称、该数据图形的样式配置、标签配置的对象。具体见[data文档] (http://echarts.baidu.com/option.html#series-pie.data)

ECharts 中的饼图通过设置 `roseType` 就可以显示成南丁格尔图。

```
roseType: 'angle'

```

南丁格尔图会通过半径表示数据的大小。


### 阴影的配置
ECharts 中有一些通用的样式，诸如阴影、透明度、颜色、边框颜色、边框宽度等，这些样式一般都会在系列的 `itemStyle` 里设置。例如阴影的样式可以通过下面几个配置项设置：

```javascript
itemStyle: {
    shadowBlur: 200,// 阴影的大小
    shadowOffsetX: 0,// 阴影水平方向上的偏移
    shadowOffsetY: 0,// 阴影垂直方向上的偏移
    shadowColor: 'rgba(0, 0, 0, 0.5)' // 阴影颜色
}
```
<img src="images/13_42_47__08_24_2018.jpg" width="300" title="南丁格尔图"/>

`itemStyle`的`emphasis`是鼠标`hover`时候的高亮样式。上面的示例里是正常的样式下加阴影，但是可能更多的时候是hover的时候通过阴影突出。
就需要如下的设置：

```javascript
itemStyle: {
    emphasis: {
        shadowBlur: 200,// 阴影的大小
        shadowOffsetX: 0,// 阴影水平方向上的偏移
        shadowOffsetY: 0,// 阴影垂直方向上的偏移
        shadowColor: 'rgba(0, 0, 0, 0.5)' // 阴影颜色
    }
}
```
 在鼠标悬浮到图形元素上时，一般会出现高亮的样式。默认情况下，高亮的样式是根据普通样式自动生成的。但是高亮的样式也可以自己定义，主要是通过 emphasis 属性来定制。emphsis 中的结构，和普通样式的结构相同,例如：

```javascript
option = {
    series: {
        type: 'scatter',
        // 普通样式。
        itemStyle: {
            // 点的颜色。
            color: 'red'
        },
        label: {
            show: true,
            // 标签的文字。
            formatter: 'This is a normal label.'
        },
        // 高亮样式。
        emphasis: {
            itemStyle: {
                // 高亮时点的颜色。
                color: 'blue'
            },
            label: {
                show: true,
                // 高亮时标签的文字。
                formatter: 'This is a emphasis label.'
            }
        }
    }
}

```

###  深色背景和浅色标签

如果我们需要把整个主题改成深色主题，这就需要改背景色和文本颜色。

背景色是全局的，所以直接在 `option`下设置 <span style="color: #337ab7;">backgroudColor</span>

```javascript
setOption({
    backgroundColor: '#2c343c'
})
```
文本的样式可以设置全局的 <span style="color: #337ab7;">textStyle</span>。

```javascript
setOption({
    textStyle: {
        color: 'rgba(255, 255, 255, 0.3)'
    }
})
```
也可以每个系列分别设置，每个系列的文本样式设置在 <span style="color: #337ab7;">label.textStyle</span>

```javascript
label: {
    textStyle: {
        color: 'rgba(255, 255, 255, 0.3)'
    }
}
```
饼图的话还要将标签的视觉引导线的颜色设为浅色
```
labelLine: {
    lineStyle: {
        color: 'rgba(255, 255, 255, 0.3)'
    }
}
```

<img src="images/themeSet.png" width="300" title="南丁格尔图"/>

### 调色盘

调色盘，可以在 option 中设置。它给定了一组颜色，图形、系列会自动从其中选择颜色。 可以设置全局的调色盘，也可以设置系列自己专属的调色盘。

```javascript
option = {
    // 全局调色盘。
    color: ['#c23531','#2f4554', '#61a0a8', '#d48265', '#91c7ae','#749f83',  '#ca8622', '#bda29a','#6e7074', '#546570', '#c4ccd3'],

    series: [{
        type: 'bar',
        // 此系列自己的调色盘。
        color: ['#dd6b66','#759aa0','#e69d87','#8dc1a9','#ea7e53','#eedd78','#73a373','#73b9bc','#7289ab', '#91ca8c','#f49f42'],
        ...
    }, {
        type: 'pie',
        // 此系列自己的调色盘。
        color: ['#37A2DA', '#32C5E9', '#67E0E3', '#9FE6B8', '#FFDB5C','#ff9f7f', '#fb7293', '#E062AE', '#E690D1', '#e7bcf3', '#9d96f5', '#8378EA', '#96BFFF'],
        ...
    }]
}
```

### <p style="color: red">ECharts 中的样式设置注意点</p>

在 ECharts4 以前，高亮和普通样式的写法，是这样的：

```javascript
option = {
    series: {
        type: 'scatter',
        itemStyle: {
            // 普通样式。
            normal: {
                // 点的颜色。
                color: 'red'
            },
            // 高亮样式。
            emphasis: {
                // 高亮时点的颜色。
                color: 'blue'
            }
        },
        label: {
            // 普通样式。
            normal: {
                show: true,
                // 标签的文字。
                formatter: 'This is a normal label.'
            },
            // 高亮样式。
            emphasis: {
                show: true,
                // 高亮时标签的文字。
                formatter: 'This is a emphasis label.'
            }
        }
    }
}
```

这种写法 仍然被兼容，但是，不再推荐。事实上，多数情况下，使用者只会配置普通状态下的样式，而使用默认的高亮样式。所以在 ECharts4 中，支持不写 normal 的配置方法（即本文开头的那种写法），使得配置项更扁平简单。

## 三、用到的ECharts的API

### echarts
  全局 echarts 对象，在 script 标签引入 echarts.js 文件后获得，或者在 AMD 环境中通过 require('echarts') 获得

1、 echarts.init()

创建一个 ECharts 实例，返回 echartsInstance, 不能在单个容器上初始化多个 ECharts 实例。

**参数**

- dom

 实例容器，一般是一个具有高宽的div元素。

 注：如果div是隐藏的，ECharts 可能会获取不到div的高宽导致初始化失败，这时候可以明确指定div的style.width和style.height，或者在div显示后手动调用 echartsInstance.resize 调整尺寸。

ECharts 3 中支持直接使用canvas元素作为容器，这样绘制完图表可以直接将 canvas 作为图片应用到其它地方，例如在 WebGL 中作为贴图，这跟使用 echartsInstance.getDataURL 生成图片链接相比可以支持图表的实时刷新。

- theme

 应用的主题。可以是一个主题的配置对象，也可以是使用已经通过 echarts.registerTheme 注册的主题名称。

- opts

 附加参数。有下面几个可选项：

 + devicePixelRatio

    设备像素比，默认取浏览器的值window.devicePixelRatio。

 + renderer

    渲染器，支持 'canvas' 或者 'svg'。参见 使用 <a href="http://echarts.baidu.com/tutorial.html#%E4%BD%BF%E7%94%A8%20Canvas%20%E6%88%96%E8%80%85%20SVG%20%E6%B8%B2%E6%9F%93"> Canvas 或者 SVG 渲染 </a>。

 + width

    可显式指定实例宽度，单位为像素。如果传入值为 null/undefined/'auto'，则表示自动取 dom（实例容器）的宽度。

 + height

    可显式指定实例高度，单位为像素。如果传入值为 null/undefined/'auto'，则表示自动取 dom（实例容器）的高度。

### echartsInstance

    通过 echarts.init 创建的实例。

 2、 echartsInstance.setOption()

 ```
(option: Object, notMerge?: boolean, lazyUpdate?: boolean)
or
(option: Object, opts?: Object)
```
设置图表实例的配置项以及数据，万能接口，所有参数和数据的修改都可以通过setOption完成，ECharts 会合并新的参数和数据，然后刷新图表。

**注：** ECharts 2.x 中的通过 addData , setSeries 方法设置配置项的方式将不再支持，在 ECharts 3 中统一使用setOption

** 调用方式：**

```
chart.setOption(option, notMerge, lazyUpdate);
```
或者
```
chart.setOption(option, {
    notMerge: ...,
    lazyUpdate: ...,
    silent: ...
});
```
**参数：**

 - option

   图表的配置项和数据，具体见<a href="http://echarts.baidu.com/option.html#title" target="_blank">配置项手册</a>。

 - notMerge

   可选，是否不跟之前设置的option进行合并，默认为false，即合并。

 - lazyUpdate

   可选，在设置完option后是否不立即更新图表，默认为false，即立即更新。

 - silent

   可选，阻止调用 setOption 时抛出事件，默认为false，即抛出事件。

3、echartsInstance.clear()

 清空当前实例，会移除实例中所有的组件和图表。清空后调用 getOption 方法返回一个{}空对象。

 有时候图表会放在多个标签页里，多个图表共用一个容器，切换标签时，多个图表的配置项会合并出现在一起，如下图：

 <img src="images/tab-chart-err1.png" width="500" title=""/>
 <img src="images/tab-chart-err2.png" width="500" title=""/>

 标签页下应该呈现的正确的图表效果：

 <img src="images/tab-chart1.png" width="500" title=""/>
 <img src="images/tab-chart2.png" width="500" title=""/>

对比就可以看出，标签页1切换到标签页2，图表1的数据以及线图的颜色的配置项被合并到图表2的配置项中；
而切换到标签页2后再切回标签页1，图表2的配置项又被合并到图表1中，所以图表1的纵坐标数据出现了"%"。

<b style="color: #67c23a;">该问题的解决方案有两种：</b>

  (1) 设置图表实例的配置项以及数据时，将参数 <span style="background-color:#f9f2f4">notMerge</span>的值设置为: true;
   notMerge : 是否不跟之前设置的option进行合并，默认为false，即合并, 设置为true，即不合并。

   ```javascript
   let queUse = echarts.init(document.getElementById('queUse'))
   // 绘制图表
   if (this.queCountTypeIndex === 0) {
       queUse.setOption(this.opt1, {notMerge: true})
       or
       queUse.setOption(this.opt1, true)
   }
   if (this.queCountTypeIndex === 1) {
       queUse.setOption(this.opt2, {notMerge: true})
   }
   window.addEventListener('resize', () => {
       queUse.resize()
   })

   ```

  (2) 就是使用clear方法，每次切换标签页时先清空当前实例，再对该实例设置配置项以及数据。

  ```javascript
  let queUse = echarts.init(document.getElementById('queUse'))
  // 绘制图表
  if (this.queCountTypeIndex === 0) {
      queUse.clear()
      queUse.setOption(this.opt1)
  }
  if (this.queCountTypeIndex === 1) {
      queUse.clear()
      queUse.setOption(this.opt2)
  }
  window.addEventListener('resize', () => {
      queUse.resize()
  })

  ```

4、 echartsInstance.resize()

改变图表尺寸，在容器大小发生改变时需要手动调用。

```
(opts?: {
    width?: number|string,
    height?: number|string,
    silent?: boolean
}) => ECharts
```

**参数**

 - opts

   opts 可缺省。有下面几个可选项：

 - width

   可显式指定实例宽度，单位为像素。如果传入值为 null/undefined/'auto'，则表示自动取 dom（实例容器）的宽度。

 - height

   可显式指定实例高度，单位为像素。如果传入值为 null/undefined/'auto'，则表示自动取 dom（实例容器）的高度。

 - silent

   是否禁止抛出事件。默认为 false。

**Tip:** 有时候图表会放在多个标签页里，那些初始隐藏的标签在初始化图表的时候因为获取不到容器的实际高宽，可能会绘制失败，因此在切换到该标签页时需要手动调用 resize 方法获取正确的高宽并且刷新画布，或者在 opts 中显示指定图表高宽。

<p style="color:red;">在vue组件中调用echartsInstance.resize()时，为了确保获取到容器的实际高宽，可以在 this.$nextTick()的回调函数中使用 resize 方法.</p>

### events

在 ECharts 中主要通过 on 方法添加事件处理函数。

在 ECharts 中事件分为两种类型，一种是用户鼠标操作点击，或者 hover 图表的图形时触发的事件，还有一种是用户在使用可以交互的组件后触发的行为事件，例如在切换图例开关时触发的 'legendselectchanged' 事件（这里需要注意切换图例开关是不会触发'legendselected'事件的），数据区域缩放时触发的 'datazoom' 事件等等。

鼠标事件包括'click'，'dblclick'，'mousedown'，'mouseup'，'mouseover'，'mouseout'，'globalout'，'contextmenu'。

```javascript
// 使用指定的配置项和数据显示图表。
myChart.setOption(option);
// 处理点击事件并且跳转到相应的百度搜索页面
myChart.on('click', function (params) {
    window.open('https://www.baidu.com/s?wd=' + encodeURIComponent(params.name));
});

```

所有的鼠标事件包含参数 params，这是一个包含点击图形的数据信息的对象，如下格式：

<code>{
    // 当前点击的图形元素所属的组件名称，
    // 其值如 'series'、'markLine'、'markPoint'、'timeLine' 等。
    componentType: string,
    // 系列类型。值可能为：'line'、'bar'、'pie' 等。当 componentType 为 'series' 时有意义。
    seriesType: string,
    // 系列在传入的 option.series 中的 index。当 componentType 为 'series' 时有意义。
    seriesIndex: number,
    // 系列名称。当 componentType 为 'series' 时有意义。
    seriesName: string,
    // 数据名，类目名
    name: string,
    // 数据在传入的 data 数组中的 index
    dataIndex: number,
    // 传入的原始数据项
    data: Object,
    // sankey、graph 等图表同时含有 nodeData 和 edgeData 两种 data，
    // dataType 的值会是 'node' 或者 'edge'，表示当前点击在 node 还是 edge 上。
    // 其他大部分图表中只有一种 data，dataType 无意义。
    dataType: string,
    // 传入的数据值
    value: number|Array
    // 数据图形的颜色。当 componentType 为 'series' 时有意义。
    color: string
}</code>

** 如何区分鼠标点击到了哪里：**

```javascript
myChart.on('click', function (params) {
    if (params.componentType === 'markPoint') {
        // 点击到了 markPoint 上
        if (params.seriesIndex === 5) {
            // 点击到了 index 为 5 的 series 的 markPoint 上。
        }
    }
    else if (params.componentType === 'series') {
        if (params.seriesType === 'graph') {
            if (params.dataType === 'edge') {
                // 点击到了 graph 的 edge（边）上。
            }
            else {
                // 点击到了 graph 的 node（节点）上。
            }
        }
    }

});
```