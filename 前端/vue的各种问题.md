# [__ob__: Observer]

**问题描述：**

- 带有 `__ob__: Observer` 数组后缀的就是没有办法取到值，但是 `console.log` 却能看得到值

- 在我们操作这个数据的时候，如果想要单独拿这个数据里面的值，就会返回 `undefined`



**问题产生原因：** `__ob__: Observer` ，这个后缀其实是Vue监控变量产生的，一般是 `不可以被枚举` 的。



**问题解决：**

- 法1：

  - `__ob__: Observer` 是 Vue 对数据监控添加的属性，如果想去掉可以采用对象赋值的方式：**Object.assign({ },this.owner)**
  - Object.assign() 方法将所有可枚举和自有属性从一个或多个源对象复制到目标对象，返回修改后的对象。

- 法2：list 里面存放的就是那些带有 `__ob__: Observer` 的数据，那么可以使用：**`JSON.parse(JSON.stringify(this.list))`** 来进行解决

- 法3：设一个延迟等去取完：

  ~~~js
  mounted() {
   setTimeout(()=>{
   //这里就写你要执行的语句即可，先让数据库的数据加载进去数组中你在从数组中取值就好了
   },800)
   }
  ~~~

  