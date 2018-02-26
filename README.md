# vueRouter-note
My note for learning vueRouter !

```
路由：

  -- 传统开发：我们的路径是由“文件夹名字和文件名结合”的。
  -- 前端路由：一般情况下只在一个页面进行交互和跳转（SPA：单页应用）
              简而言之就是不刷新页面，根据用户点击来控制和显示页面的输出
              就和整个页面是纯用ajax开发的一样
```
```
SPA : 
  -- SPA开发就是后端提供接口，前端直接通过接口调用和渲染，
     在这个时候页面没有了后端的渲染，而交给了前端，前端来通过接口渲染数据
优点：
  -- 职责变得更加清晰
  -- 开发更加便利
  -- 体验更好，在移动端的体验和速度都更好

弊端：
  -- 不利于SEO（搜索引擎优化）
  -- 渲染速度会比服务端渲染（SSR）要慢
  -- 数据前端处理起来很麻烦，后端也很麻烦

解决速度问题：
  -- SSR：服务端渲染（同步），这块内容是咱们在开发时非常重要的东西，在外称之为“直出”，首屏渲染
在服务端直接把前端和后端的代码混用输出
是对访问量巨大的页面会进行ssr(一般首页的访问量是最大的)

一般情况下会使用Node来做开发：
Node是后端，此时后端在前端手上，而用Node可以控制页面是服务端渲染还是单页应用式的渲染

```
```
路由和Iframe区别：
  vue路由：
     1.不进行页面任何刷新
     2.他不需要指定URL
  iframe：
     1.通过URL刷新页面
     2.需要指定
```

###1.路由基础
```javascrpt
router-link :使用router-link组件来导航,在页面会替换为a标签将需要跳转的路由写在to当中
             <router-link to="/home">Home</router-link>
             <router-link to="/subPage">subPage</router-link>
router-view:路由出口， 路由匹配到的组件将渲染在这里

路由核心基础流程：
  //路由就是通过用户点击路由加载相对应的组件
  //第一步：定义组件
  templateA :
    <div>Home page</div>
  templateB :
    <div>subPage</div>
  //第二步：定义路由
  let routes = [
      {path:"/home",component:Home},
      {path:"/subPage",component:Subpage}
  ]
  //第三步：创建router实例，然后传入定义好的路由（`routes`配置）
  //每个路由都应该映射一个组件，component，可以是组件配置对象也可以通过Vue.extend()创建的组件构造器
  let router = new VueRouter({
      routes:routes
  });
  //第四步：创建和挂载实例，从而让整个应用都有路由功能
  const app = new Vue({router:router}).$mount("#app");

```
```
2.动态路由
  应用场景举例：我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。
  配置方法：
    routes: [
      // 动态路径参数 以冒号开头
      { path: '/userA/:id', component: User }
      // 正则匹配
      { path: '/userB/:reg(\\d+)', component: User }
      // 路由嵌套时访问多段动态路径参数
      // this.$route.params对象  --> {username:"adsa",post_id:345}
      { path: '/userC/:username/post/:post_id', component: User }
    ]

 console.log($route)  : 
    {
       meta : {},
       path : "/userA/123",
       hash : "",
       query : "",
       params : {
          id : 123
       },
       fullPath : "/userA/123",
       matched : [
          {
             path : "/userA/:id",
             components : {},
             instances : {},
             meta : {}
          }
       ] 
    }
  //获取动态路径参数  --> this.$route.params.id

  响应路由参数变化：
    -- 当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。
       因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。
       不过，这也意味着组件的生命周期钩子不会再被调用。
    -- 想对路由参数的变化作出响应的话，使用 watch（监测变化） $route 对象：
       watch:{
          '$route'(to,from){
              // 对路由变化作出响应...
              console.log('从'+from.params.id+'到'+to.params.id);
          }
       }
    -- 或者使用 beforeRouteUpdate 守卫：
       beforeRouteUpdate (to, from, next) {
            // react to route changes...
            // don't forget to call next()
       }

 
```
```
3.嵌套路由
    routes: [
        {
           name : "user"
           path : "/user/:id/", 
           component : User, 
           children : [
              { name : "UserHome",   path: '',        component: UserHome    },
              { name : "UserProfile",path: 'profile', component: UserProfile },
              { name : "UserPosts",  path: 'posts',   component: UserPosts   }
           ]
         }
    ]

```
```
4.编程式路由
  在 Vue 实例内部，可以通过 $router 访问路由实例。
  因此可以调用 this.$router.push、this.$router.replace、this.$router.go

  push : 类似 window.history.pushState
         这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

      // 字符串
      router.push('home')
      // 对象 /home
      router.push({ path: 'home' })
  
      // 命名的路由 如果提供的是path,params会被忽略
      router.push({ name: 'user', params: { userId: 123 }})
      router.push({ path: `/user/${userId}` }) // -> /user/123
      // 带查询参数，变成 /register?plan=private
      router.push({ path: 'register', query: { plan: 'private' }})

replace : 类似 window.history.replaceState
          跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，
          而是跟它的方法名一样 —— 替换掉当前的 history 记录。

go : 类似 window.history.go
     这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步

      // 在浏览器记录中前进一步，等同于 history.forward()
      router.go(1)
      // 后退一步记录，等同于 history.back()
      router.go(-1)
      // 前进 3 步记录
      router.go(3)
      
      // 如果 history 记录不够用，那就默默地失败呗
      router.go(-100)
      router.go(100)
```
```
5.命名路由
  
  routes: [
    { path: '/user/:userId', name: 'user', component: User }
  ]

  //下面两行代码是一回事
  <router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
  router.push({ name: 'user', params: { userId: 123 }})

```
```
6.命名视图
    <router-view></router-view>
    <router-view name="a" class="viewLeft"></router-view>
    <router-view name="b" class="viewRight"></router-view>

    routes: [
       {
          path: '/',
          components: { default : Foo, a : Bar, b : Baz }
        }
     ]

```
```
7.路由重定向

    routes: [
       { path: '/', components: temp },
       { path: '/aaa', components: tempA },
       { path: '/bbb', components: tempB },
       { path: '/ccc', redirect : '/aaa'}, //如果当前路由是 /ccc就自动跳转到 /aaa
       { 
          path: '/ddd/:id', 
          redirect : argument => {        
             const {hash,params,query} = argument;
             if(params.id === '001') {
                 return '/bbb'
             }
          }}
     ]
```
```
8. alias 相对于模板的别名
   什么是别名 ? 
      简单来说就是：这个人的大名叫张三，小名叫二狗子，我们找张三能找到这个人，同样找二狗子也能找到，
                   那么二狗子就是他的别名，当然他也可以有很多别名。
   用法 ： 
        <router-link to="/zhangsan">Home</router-link>
        <router-link to="/ergouzi">ergouzi</router-link>
        <router-link to="/shidan">shidan</router-link>
        routes: [
           { path: '/zhangsan', components: zhangsan,alias:['/ergouzi','/shidan'] }
        ]
```
```
9. 处理 404 页面
    STEP_1 : 准备一个 404 页面的组件
    STEP_2 : 
             import pageNotFound from '../pages/pageNotFound.vue'

             // 方式一 ：当输入的路径匹配不到的时候返回首页
             { path: '*',     name: 'pageNotFound', redirect : '/'          } 

             // 方式二 ：当输入的路径匹配不到的时候进入 404 页面
             { path: '*',     name: 'pageNotFound', component: pageNotFound }

    **注意** ： 
              内容 : 这条匹配404的路径对象一定要写在该数组的最后一个位置
              原因 ： 因为vue-router查询路径对象的方式就好比 if --> else if --> else 

```
```
10. 路由钩子函数（待续）

    钩子作用 ：主要用来通过跳转或取消的方式守卫导航
    植入方式 ：全局 / 单个路由独享 / 组件内。
    **注意** ：动态路径参数（params）和查询字段（query）的改变并不会触发 进入/离开 的钩子函数，
               可以通过 watch --> $route 对象 或者 使用 beforRouteUpdate 钩子

    1.全局路由钩子




    2.单个路由内的钩子
    3.组件内的路由钩子
      export default {
          data () {
              return {
                ....
              }
          },
          beforeRouteEnter (to, from, next) {
              // 在渲染该组件的对应路由被 confirm 前调用
              // 不！能！获取组件实例 `this`
              // 因为当守卫执行前，组件实例还没被创建
              console.log(to);
              console.log(from);
              console.log(from.params.studentId)
              // 不过，可以通过传一个回调给 next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
              next(vm => {
                  console.log(vm);
              })  
          },
          beforeRouteUpdate (to, from, next) {
              // 在当前路由改变，但是该组件被复用时调用
              // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
              // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
              // 可以访问组件实例 `this`
              this.studentId = to.params.studentId
              console.log(this.studentId)
              next()
          },
          beforeRouteLeave (to, from , next) {
              // 导航离开该组件的对应路由时调用
              // 可以访问组件实例 `this`
              // 这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消离开。
              console.log(to);
              console.log(from);
              const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
              if (answer) {
                  next()
              } else {
                  next(false)
              }
           }
      }
      

```
```
11.HTML5 history 模式配置



```
```
12.源码分析：
    router分析：http://cnodejs.org/topic/58d680c903d476b42d34c72b
    “滴滴”源码分析：https://github.com/DDFE/DDFE-blog（分析得很透彻）

实现过程技术点：
    Vue 插件机制
    响应式数据机制
    Vue 渲染机制
    地址变更监听
```


