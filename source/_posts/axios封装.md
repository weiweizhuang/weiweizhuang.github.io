---
title: 封装axios 请求
date: 2019-11-13 23:59
tags: [vue,前端]
categories: 
    - [前端, vue, 组件封装]
---

## axios封装

	```javascript
		import axios from 'axios'
	    import { MessageBox, Message } from 'element-ui'
	    // import store from '@/store'
	
	    // create an axios instance
	    const service = axios.create({
	      baseURL: '', // url = base url + request url
	      // withCredentials: true, // send cookies when cross-domain requests
	      headers: {
	        'Content-Type': 'application/json',
	        'lang': 'zh-CN,zh;q=0.9'
	      },
	      timeout: 5000 // request timeout
	    })
	
	        // request interceptor
	        service.interceptors.request.use(
	          config => {
	        // do something before request is sent
	        if (sessionStorage.getItem("token")) {
	          // let each request carry token
	          // ['x-auth-token'] is a custom headers key
	          // please modify it according to the actual situation
	          config.headers['x-auth-token'] = sessionStorage.getItem("token")
	        }
	    	return config
	      },
	      error => {
	        // do something with request error
	        console.log(error) // for debug
	        return Promise.reject(error)
	          }
	)
	
	// response interceptor
	
	service.interceptors.response.use(
	
	  response => {
	const res = response.data
	if(response.headers['x-auth-token']) {
	    sessionStorage.setItem('token', response.headers['x-auth-token']);
	}
	// if the custom code is not 20000, it is judged as an error.
	    if (response.status !== 200 ) {
	        Message({
	            message: res.msg || 'Error',
	            type: 'error',
	            duration: 5 * 1000
	        })
	
	        if (res.code === 508 || res.code === 502 || res.code === 503) {
	            
	        }
	        return Promise.reject(new Error(res.msg || 'Error'))
	    } else {
	
	        return  Promise.resolve(res)
	    }
	      },
	      error => {
	        console.log('err' + error) // for debug
	        Message({
	          message: error.msg,
	          type: 'error',
	          duration: 5 * 1000
	        })
	        return Promise.reject(error)
	      }
	    )
	
	    export default service
```
##先统一在一个文件里面请求
```
	import service from '../utils/ajax'
	import API from '../utils/API'
	
	export const qesLogin = (data) => {
	    return service({
	        url: API.USER.LOGIN,
	        method: 'post',
	        data
	    })
	}
```
## 使用例子
	![aixos请求例子](/images/axios-request.png)
