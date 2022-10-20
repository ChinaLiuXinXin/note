# axios

axios的安装

```javasc
$ npm install axios
```

执行get请求

```javascript
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
//在成功时调用
  .then(function (response) {
    console.log(response);
  })
//在失败时调用
  .catch(function (error) {
    console.log(error);
  });
```

执行post请求

```javascript
//可以在post方法上加入相应参数
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

拦截器

请求拦截器：对请求一定处理，如携带token，加进度条等

```javascript
// 请求拦截器
requests.interceptors.request.use((config) => {
    nprogress.start();
    // 请求时带上token
    let token = localStorage.getItem("token")
    // 判断token是否存在
    if(token != ""){
        config.headers.Authorization = token
    }
    return config;
},(error) => {
    alert(error.message);
    // 终止Promise链
    return new Promise();
})

// 响应拦截器
requests.interceptors.response.use((config) => {
    nprogress.done();
    return config;
},(error) => {
    alter(error.message);
    return new Promise();
})
```

​	**axios发送请求参数问题**

​	data：携带请求体

​	params：通过拼接字符串传参数