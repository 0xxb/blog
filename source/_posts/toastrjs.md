---
title: Toastr.js
tags: 技术
date: 2018-08-21 03:52
comments: true
---

<img src="https://cdn.wispx.cn/blog/2018/08/21/2b6a4ebdeedb744c.jpeg" width="100%">
# Toastr

### 项目介绍
Toastr.js 一款简单的非堵塞消息通知插件。本插件依赖Jquery

### 演示
<a href="https://wispx.gitee.io/toastr/demo.html" target="_blank">https://wispx.gitee.io/toastr/demo.html</a>

### 仓库地址
码云：<a href="https://gitee.com/wispx/toastr" target="_blank">https://gitee.com/wispx/toastr</a><br />Github：<a href="https://github.com/wisp-x/toastr" target="_blank">https://github.com/wisp-x/toastr</a>

### 使用方法
下载项目，引入jquery和src目录下的css/toastr.css和js/tosatr.js

```html
<link rel="stylesheet" href="css/toastr.min.css">
<script src="js/jquery-3.3.1.min.js"></script>
<script src="js/toastr.min.js"></script>
```

##### 全局配置
```javascript
$.toastr.config({
  time: 3000,
  position: 'top-right',
  size: '',
  callback: function () {}
});
```

配置说明：
<table width="100%">
    <thead>
    <tr>
        <th>配置名</th>
        <th>配置说明</th>
        <th>可选参数</th>
        <th>默认值</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>time</td>
        <td>关闭时间(毫秒)</td>
        <td>1000~999999之间的纯数字</td>
        <td>3000</td>
    </tr>
    <tr>
        <td>position</td>
        <td>显示位置</td>
        <td>top-left,top-center,top-right,right-bottom,bottom-center,left-bottom</td>
        <td>top-right</td>
    </tr>
    <tr>
        <td>size</td>
        <td>大小</td>
        <td>lg,sm,xs</td>
        <td>空(正常大小)</td>
    </tr>
    <tr>
        <td>callback</td>
        <td>默认关闭后的回调</td>
        <td>function</td>
        <td>无</td>
    </tr>
    </tbody>
</table>

---

##### 显示一个成功通知，并设置一个关闭后的回调
```javascript
$.toastr.success('执行成功', {
    callback: function() {
      console.log('执行回调')
    }
});
```

##### 在左上角显示一个信息通知
```javascript
$.toastr.info('有新消息了', {
    position: 'top-left'
});
```

##### 显示一个警告通知，1秒后关闭
```javascript
$.toastr.warning('警告，禁止操作!', {
    time: 1000
});
```

##### 显示一个大小为sm的失败通知
```javascript
$.toastr.error('执行失败!', {
    size: 'sm'
});
```

##### 清除所有通知
```javascript
$.toastr.clear();
```

### License

- MIT license