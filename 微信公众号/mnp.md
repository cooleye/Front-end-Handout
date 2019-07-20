# 微信小程序开发

## 一、微信小程序介绍
* 什么是微信小程序
* 发展历程
* 小程序技术发展史  WeixinJSBridge -> js-sdk -> 小程序
* 小程序和普通网页开发的区别

#### 小程序开发流程
* 注册微信小程序账号
    * https://mp.weixin.qq.com/wxopen/waregister?action=step1
    * 安装开发者工具
    * 创建项目 hello world项目
* 小程序信息完善
* 开发小程序
* 提交审核和发布
* 
## 二、小程序入门
#### 介绍目录结构

#### 介绍小程序代码类型
wxml、js、json、wxss


## 常用组件
view、swiper、icon、video、map、navigator
## 常用api
request、navigateTo

## 小项目练习

## 小程序云开发


## wx-charts图表
##### Github:https://github.com/xiaolin3303/wx-charts
#### 使用方法
1、引入 wx-charts
   `var wxCharts = require('../../utils/wxcharts.js');`
   
2、在页面上添加canvas标签
    ` <canvas canvas-id="lineCanvas" style='width:100%;height:200px;'></canvas>`
    
3、在js文件中使用
```
var simulationData = {
      "categories": ["2016-1", "2016-2", "2016-3", "2016-4", "2016-5"],
      "data": [15,12, 15, 16, 19]
    }
    lineChart = new wxCharts({
      canvasId: 'lineCanvas',  //微信小程序canvas-id
      type: 'line', //图表类型，可选值为pie, line, column, area, ring, radar
      categories: simulationData.categories, //数据类别分类(饼图、圆环图不需要) 
      animation: true, //是否动画展示，默认为true
      //数据列表
      series: [{
        name: '成交量1',//数据名称
        data: simulationData.data,//数据值
        format: function(val, name) {  //格式化数据显示
          return val.toFixed(2) + '万';
        }
      }],
      //X轴配置
      xAxis: { 
        disableGrid: true
      },
      //Y轴配置
      yAxis: {
        title: '成交金额 (万元)',
        format: function(val) {
          return val.toFixed(2);
        },
        min: 0
      },
      width: windowWidth, //canvas宽度，单位为px
      height: 200,  //canvas高度，单位为px
      dataLabel: false, //是否在图表中显示数据内容值，默认true
      dataPointShape: true, //是否在图表中显示数据点图形标识，默认true
      extra: { //其他非通用配置项
        lineStyle: 'curve'
      }
    });
```
##### 参数说明：https://github.com/xiaolin3303/wx-charts/issues/56


## 
wepy

uni-app

mpvue

taro
...


## wepy
官网：https://wepyjs.github.io/wepy-docs/1.x/#/
Github：
开发资源汇总：https://github.com/aben1188/awesome-wepy
#### 安装使用 
`npm install wepy-cli -g`

#### 查看项目模板
使用 wepy list 查看项目模板

#### 创建项目
`wepy init standard myproject`
#### 安装依赖
```
cd myproject
npm install
```
#### 启动，开启实时编译
`npm run dev`

#### 导入微信开发者工具
打开微信开发者工具，选择导入项目，导入项目根目录下下的weapp


