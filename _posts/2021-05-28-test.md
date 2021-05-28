---
toc: false
layout: post
description: MES 
categories: [MES]
title: MES
---
# Vue环境配置

- 下载[vsCode](https://code.visualstudio.com/Download)
- 下载[npm](https://nodejs.org/zh-cn/download/)

```bash
# 检查版本号
npm -V 

# 下载vue框架
npm install -g @vue/cli

# 检查vue是否安装成功
vue -V

# 当前文件夹下新建项目
vue create demo
# 或
vue ui
# element-ui组件库安装
npm i element-ui -S

# echarts组件库安装
npm install echarts --save

# 安装vue-router组件
npm install vue-router 

# 运行程序
npm run serve
```



# 使用

环境配置好后可以开始写页面了.

1. **配置导航栏菜单**, 根据自己的模块分级(这边只做到三级，三级以上还需要修改)，修改文件`/src/views/Main.vue`.

```javascript
          menuData: [{
              //一级模块
              text: "设备管理",
              index: "",
              //二级模块
              children:[
                  {
                    text: '设备运行管理',
                    index:'',
                    //三级模块
                    children:[
                        {
                            text: '设备状态监控',
                            index:'/equipment_status_monitoring',
                        },{
                            text: '设备运行记录',
                            index: '/equipment_operation_record',
                        },{
                            text: '设备运行报表',
                            index: '/equipment_operation_report',
                        }
                    ]
                  },
                  {
                     //to do
                  }
              ]
          }
```

在`//to do`中模仿`设备运行管理`中的内容，添加对应模块分级，index部分表示有页面的部分，请使用模块对应的英文加 下划线**_** . 修改完后运行程序验证导航栏是否有问题.

2. **添加页面和路由**

##### 添加页面

> ​	在`src/views`路径下新建文件夹, 名称以模块名对应的英文命名如**设备管理**命名为**device_management**，在此目录下创建页面文件, 文件名称与1中`index`名称一致如`equipment_operation_report.vue`

---

##### 添加路由

> src/router/index.js文件中添加路由,示例如下

```javascript
export default new VueRouter({
    routes: [
        {
            path: '/equipment_status_monitoring',
            component:()=>import("@/views/device_management/equipment_status_monitoring")
        },
        {
            path: '/equipment_operation_record',
            component:()=>import("@/views/device_management/equipment_operation_record")
        },
        {
            path: '/equipment_operation_report',
            component:()=>import("@/views/device_management/equipment_operation_report")
        },
        //以下为添加的内容
        {
            // path与1中index内容对应
            path: '/process_report'
            // import内容为process_report.vue路径, 即页面文件
            component:()=>import("@/views/device_management/process_report.vue")
        }   
        ]

}
```



3. **编辑页面**

以上步骤完成后, 直接编辑对应的页面文件即可. 使用以下两个组件库找到对应的空间将代码粘贴上去即可，之后有什么布局问题可以百度或者讨论一下. 

[element-ui](https://element.eleme.io/#/zh-CN/component/icon):按键，表格，对话框等控件

[echarts](https://echarts.apache.org/examples/en/index.html)：图形，饼状图，散点图，柱状图等demo

[vue](https://cn.vuejs.org/v2/guide/)：vue框架的使用手册, 作为资料查询





如将以下代码粘贴到你的页面文件中保存后即可显示一个select选择器

```javascript
<template>
  <el-select v-model="value" placeholder="请选择">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value">
    </el-option>
  </el-select>
</template>

<script>
  export default {
    data() {
      return {
        options: [{
          value: '选项1',
          label: '黄金糕'
        }, {
          value: '选项2',
          label: '双皮奶'
        }, {
          value: '选项3',
          label: '蚵仔煎'
        }, {
          value: '选项4',
          label: '龙须面'
        }, {
          value: '选项5',
          label: '北京烤鸭'
        }],
        value: ''
      }
    }
  }
</script>
```

# 如图:

![image-20210527204243574](C:\Users\梁望\AppData\Roaming\Typora\typora-user-images\image-20210527204243574.png)
