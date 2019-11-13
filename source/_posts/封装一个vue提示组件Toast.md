---
title: 封装一个Toast插件
date: 2019-11-13 23:21
tags: [vue,前端]
categories: 
    - [前端, vue, 组件封装]
---



## 开始封装一个提示插件

>>> ##### 	基于elementUI icon 组件封装
### vue模版文件
``` vue
	toast/index.vue
		<template>
	    <div class="wrap" v-if="showWrap" :class="showContent ?'fadein':'fadeout'">
	          <div><i :class="icon" class="toastIcon"></i></div>&nbsp;&nbsp;&nbsp;{{text?				text : '请输入提示语'}}
	      </div>
	    </template>
	    <script>
export default {
  mounted(){
    switch (this.icon) {
      case 1:
        this.icon = 'el-icon-success'
        break;
      case 2:
        this.icon = 'el-icon-error'
        break;
      case 3:
        this.icon = 'el-icon-warning'
        break;
      default:
        this.icon = this.icon
        break;
    }
  }
}
</script>
<style scoped>
.wrap {
  width: 260px;
  min-height: 35px;
  line-height: 35px;
  text-align: center;
  font-size: 18px;
  position: fixed;
  left: 50%;
  top: 50%;
  background: rgba(0, 0, 0, 0.35);
  padding: 10px;
  border-radius: 5px;
  transform: translate(-50%, -50%);
  color: #fff;
}
i.toastIcon {
  font-size: 30px;
  vertical-align: middle;
}
i.el-icon-success {
  color:greenyellow;
}
i.el-icon-error {
  color:red;
}
.fadein {
  animation: animate_in 0.25s;
}
.fadeout {
  animation: animate_out 0.25s;
  opacity: 0;
}
@keyframes animate_in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
@keyframes animate_out {
  0% {
    opacity: 1;
  }
  40% {
    opacity: .6;
  }
  80% {
    opacity: .3;
  }
  100% {
    opacity: 0;
  }
}
</style>

```



### js 文件
```javascript
toast/index.js
import vue from 'vue'

import toastComponent from './toast.vue'

const ToastConstructor = vue.extend(toastComponent)

/**
 * 
 * @param {String} text 提示语
 * @param {Number, String} icon icon图标类
 * @param {Number} duration 延迟时间
 * @param {Function} callBack 回调函数
 */
function showToast(text, icon='ssss', duration = 2000,callBack) {
    const toastDom = new ToastConstructor({
        el: document.createElement('div'),
        data() {
            return {
                text, 
                icon,
                callBack,
                showWrap: true,    // 是否显示组件
                showContent: true  // 作用:在隐藏组件之前,显示隐藏动画
            }
        }
    })
    document.body.appendChild(toastDom.$el) 
    setTimeout(() => { toastDom.showContent = false }, duration)
    setTimeout(() => { 
        toastDom.showWrap = false 
        if(callBack && typeof callBack  === 'function') {
            toastDom.callBack()
        } else if(callBack) {
            throw new Error('callBack 参数必须要传函数！');
        }
    }, duration)
}


// 注册为全局组件的函数
function registryToast() {
    vue.prototype.$toast = showToast
}
export default registryToast
```

### mian.js
```JavaScript
	// 在main.js文件里面注册到vue实例中去，这样全局就可以使用了
	main.js
	import toastRegistry from './components/toast/index'
	
	Vue.use(toastRegistry)
	
```

 ~~有一个地方是百度来，加以改造了一下，如发现有冒犯的地方，可以联系我删除~~
