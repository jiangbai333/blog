---
title: Vue.js常见错误及其解决方法
date: '2017/02/21 13:10:00'
categories:
  - 编程语言
  - javascript
  - Vue.js
tags:
  - Vue.js
  - 框架
  - javascript
abbrlink: 61ffff07
---
## Component template should contain exactly one root element
vue.js 2.0要求每个组件需拥有有一个根元素。可以在最外层包一个div来解决这个问题
<!-- more -->
```html
<script type="text/x-template" id="cp1">
    <div>script生成的组件   {{msg}}</div>
</script>

<!-- 正确 -->
<script type="text/x-template" id="cp2">
    <div>script生成的组件   {{msg}}<cp1></cp1></div>
</script>

<!-- 错误 -->
<script type="text/x-template" id="cp2">
    <div>script生成的组件   {{msg}}</div>
	<cp1></cp1>
</script>


<div id="app">
    <cp2></cp2>
</div>

<script>
var vm = new Vue({
    el: '#app',
    data: {
        name: 'keepfool',
        age: 28
    },
    components: {
        'cp2': {
            template: '#cp2',
            data: function() {
                return {msg: "父组件"}
            },
            components: {
                'cp1': {
                    template: '#cp1',
                    data: function() {
                        return {msg: "子组件"}
                    }
                }
            }
        }
    }
})
</script>
```
