# vue

**插件**：对vue进行增强，实际上就是向vue中增加了很多功能，如指令，过滤器等。

```vue
export default{
    install(Vue,a,b,c){
        console.log(a,b,c)
    }
}
//使用
import Plugs from "./plugs"
Vue.use(Plugs,1,2,3)

//事实上插件就是一个对象，其特殊之处在于该对象内部必须要有一个install方法，install方法的第一个参数是vue对象，其他就没什么了.使用的时候还可以自己传参数进去。
```

**自定义指令**：类似v-for，v-html，v-model...之类的指令，可以根据自己的需求来自定义指令。

```vue
//全局指令，也就是注册到vc上
Vue.directive("abcd",(el) => {
  console.log(el)
})
//局部指令，也就是注册到vm上
export default {
    name:"Test",
    directives:{
      abcd(...value){
        console.log(value)
      }
    }
}
```

