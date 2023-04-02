---
title: Vue 中实现路由、请求拦截器
tags: 技术
date: 2019-08-21 09:42
comments: true
---

前后端分离模式已然成为现在的主流模式，鉴权方式从原始的 Session 到现在的 jwt、oauth2 等等方式，无论是哪一种方式，在前端，我们都要通过使用拦截器来实现权限认证等系列操作，我们来讲讲 Vue 中的路由拦截器与请求拦截器中的实现方法。

### 用到的组件
- vue-router
- axios

### 请求拦截器
  首先我们创建一个文件，用来封装 axios 的一些基础方法或配置，我把这个文件命名为 ```axios.js```
```javascript
import axios from 'axios'
import router from '../router'

axios.defaults.withCredentials = true;  // 设置cross跨域 并设置访问权限 允许跨域携带cookie信息

axios.defaults.headers.post['Content-Type'] = 'application/json'
axios.defaults.headers.put['Content-Type'] = 'application/json'

// http request 拦截器
axios.interceptors.request.use(
  config => {
    return config;
  },
  error => {
    return Promise.reject(error);
  }
);

// http response 拦截器
axios.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    return Promise.reject(error) // 返回接口返回的错误信息
  }
);

export default function () {
  return axios
}
```
http request 拦截器，即请求拦截器。  
例：当你本地存在 token 时，可以在请求拦截器中将 token 追加到请求header 里，就像这样：
```javascript
// http request 拦截器
axios.interceptors.request.use(
  config => {
    let token = localStorage.getItem('token');
	if (token) {
      config.headers.Authorization = token；
    }
    return config;
  }
);
```
服务器接收到请求后，对这个 token 进行校验，通过 token 来判定是否返回你想要的数据。  

请求拦截器写好了，那响应拦截器有什么作用呢？  

假如这个时候你正在用户中心删除某一条数据，删除的请求加上 token 发送给了服务端后，服务端给你返回了 401 状态码，表示你的 token 已经失效了，你需要重新登录。  

这时候问题来了，你不可能在每一个成功的请求里都判断一下 状态码是否是 401 或 token 是否失效吧？这时候响应拦截器就该登场了。  

```javascript
// http response 拦截器
axios.interceptors.response.use(
  response => {
    // 未登录或会话已过期
    if ('401' === response.data.code) {
      // 重定向到登录页
      router.replace({
        path: '/auth/login',
        query: {redirect: router.currentRoute.fullPath}
      })
    }
    return response;
  },
  error => {
    if (500 === error.response.status) {
	  // 服务端异常  
    }
    return Promise.reject(error) // 返回接口返回的错误信息
  }
);
```

以上示例代码，在响应拦截器中，判断了每一次服务端返回的状态码是否是 401，如若是的，将会重定向到登录页，此类场景也就是登录状态失效，需要用户重新授权登录。  

这里要注意的是，这里所说的状态码是自定义的状态，并非请求的 Status Code，如果服务端返回的 Status Code 不是 200，则会走到拦截器的 error 里。

### 路由拦截器
请求拦截器实现了，但现在还有一个问题。  
在单页面应用里，使用路由跳转页面，在切换页面没有请求的情况下，如何校验权限呢？  

路由拦截器登场，我们直接在 ```router.js``` 文件里编写路由拦截器。  
```javascript
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router);

const router = new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/user',
      name: 'user',
      component: () => import('@/views/User.vue'),
	  meta: {
	    auth: true,
		title: '用户中心'
	  }
    },
    {
      path: '/auth/login',
      name: 'login',
      component: () => import('@/views/auth/Login.vue'),
      meta: {
        auth: false
      }
    }
  ]
});

// 路由拦截器
router.beforeEach(async (to, from, next) => {
  if (to.matched.some(record => record.meta.auth) && to.meta.auth) { // 判断该路由是否需要登录权限
	let token = localStorage.getItem('token');
    if (token) { // 获取当前的 token 是否存在
      next()
    } else {
      // 不存在 token，需要重新认证
      next({
        path: '/auth/login',
        query: {
          redirect: to.fullPath
        }
      })
    }
  }
  next();
});

export default router
```

在路由文件中添加路由拦截器，如上所写。  
在每个路由中增加 meta.auth, auth = true 代表此路由页面需要进行权限认证后才可以访问，否则反之。  

每一次路由跳转都会经过路由拦截器，在拦截器中根据 meta.auth 判断是否需要鉴权。本文为了新手便于理解，去除了一些比较复杂的代码，例如 状态管理 Vuex。  

至此，一个简单的权限认证就做好了。在这两种拦截器上，不局限于只使用它来做鉴权，可以利用拦截器来实现自己其他的业务逻辑。


原创文章，转载请注明出处。
