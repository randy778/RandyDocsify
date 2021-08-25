### webpack loader配置从右往左执行
### dom中quereySelector和getElementByID的区别：静态获取和动态获取
### js事件循环规律可大致可以参考如下两套代码搞清楚：（这篇文章写得非常详细，可以作为处理此类问题的参考https://juejin.cn/post/6997968693414084644）
### return Promise.resolve()会生成2个微任务入列
```javascript
        function func1() {
            console.log('func1 start');
            return new Promise(resolve => {
                resolve('OK');
            })
        }
        function func2() {
            console.log('func2 start');
            return new Promise(resolve => {
                setTimeout(() => {
                resolve('OK');
                }, 10)
            })
        }
        console.log(1);
        setTimeout(async () => {
            console.log(2);
            await func1();
            console.log(3);
        }, 20);
        for (let i = 0; i < 90000000; i++) { } // 约 80 ms
        console.log(4);
        func1().then(() => {
            console.log(5);
        })
        func2().then(() => {
            console.log(6);
        })
        setTimeout(() => {
            console.log(7)
        }, 0);
        console.log(8);
```
```javascript
        async function f1() {
            await f2()
            console.log('f1结束')
        }
        async function f2() {
            await f3()
            console.log('f2结束')
        }
        async function f3() {
            console.log('f3结束')
        }
        f1()
        new Promise(res=>{
            console.log('new Promise')
            res()
        }).then(res=>{
            console.log('promise第一个then')
        }).then(res=>{
            console.log('promise第二个then')
        })
```
### setState本身并不是异步，只是因为react的性能优化机制体现为异步。在react的生命周期函数或者作用域下为异步，在原生的环境下为同步
### 除了qiankun，阿里系的还有icestark和Alfa的微前端框架，可以了解下看看区别
### 阿里的iconfont的使用比较简单，在官网可以查看在web端和移动端的使用方法
### 一个简单的js单利模式的实现
```javascript
    let CreateSingleton = (function(){
        let instance;
        return function(name) {
            if (instance) {
                return instance;
            }
            this.name = name;
            return instance = this;
        }
    })();

    let aa = new CreateSingleton('1')
    let bb = new CreateSingleton('2')

    console.log(aa)
    console.log(bb)
```
### async函数总是返回一个promise
```
函数体内抛出了错误，则函数执行结果为这个错误的Promise.reject()包装对象
如果函数体内返回了一个promise，则函数执行结果就是这个promise。
否则，函数执行结果为函数体内返回值的Promise.resolve()包装对象。有一种情况容易让人混淆：函数体return new Erro(1)，则执行结果相当于Promise.resolve(new Error(1))，注意return new Error(1)和throw new Error(1)的区别。
```
### async函数总是返回一个promise
```
await后总是跟一个promise，如果后面跟的不是promise，会用Promise.resolve()包装一下。
如果await表达式后跟一个fulfilled态的promise，会返回对应的值。
如果await表达式后跟一个rejected态的promise，会抛出错误，可以用.catch()方法或try...catch块捕获。
```
### await的方法同步执行，后面的代码会被作为一个微任务入队
### 一下代码了解async...await
```javascript
    const Err = async () => {
        throw new Error(42);
        // 相当于 return Promise.reject(new Error(42))
    };

    const Obj = {
        async A (){
            // 1.2 for循环中`await A()`相当于`await Promise.reject(new Error(42))`
            // 会被for循环内的catch块捕获
            // **所以第1轮for循环输出D**
            try {
                return Err();
                // 1.1 相当于 return Promise.reject(new Error(42))
                // 这行代码没有抛出错误，所以下面的catch块不会走，不会输出A
            } catch {
                console.log('A');
            }
        },
        async B (){
            // 2.2 for循环中`await B()`相当于`await Promise.resolve(undefined)`
            // 这行代码不会被for循环内的catch块捕获
            // **所以第2轮for循环输出B**
            try {
                // 2.1 相当于`await Promise.reject(new Error(42))`
                // 这行代码会被下面的catch块捕获，输出B
                await Err();
            } catch {
                console.log('B');
            }
        },
        async C (){
            // 3.2 for循环中`await C()`相当于`await Promise.resolve(undefined)`
            // 不会被for循环内的catch块捕获
            // **所以第3轮什么都不输出。但是会报错`Uncaught (in promise) Error: 42`**
            try {
                // 3.1 相当于`Promise.reject(new Error(42))`
                // 这行代码不会被下面的catch块捕获，不会输出C
                // 但是由于这个`rejected`态的promise没有被catch处理所以会报错`Uncaught (in promise) Error: 42`
                // 注意try...catch块内`Promise.reject()`和`await Promise.reject()`是不一样的。
                Err();
            } catch {
                console.log('C');
            }
        },
    };

    ( async () => {
        for( const key in Obj )
        {
            try {
                await Obj[key]();
            } catch {
                console.log('D');
            }
        }
    } )();
```
### 页面卡顿可以使用devtool里面的performance和memory来对比查看heap内存情况，排查是否属于内存泄露
### js中我们可以使用JSON.stringify搭配JSON.parse实现深拷贝，前提是需要拷贝的数组满足以下规则，否则会有问题
```
在序列化js对象时，所有函数和原型成员都会有意的在结果中省略。
此外，值为undefined的任何属性也会被跳过。最终等到的就是所有实例属性均为有效JSON数据类型的表示。
注意：如果日期格式使用JSON.stringify后会变成字符串,JSON.parse后也是字符串。
```
### qiankun框架的通信方案
```
第一种是qiankun自己提供的action通信，利用qiankun的initGloabalState方法注册一个MicroAppStateActions，然后在需要监听数据变化的地方通过onGlobalState注册监听者：简单的流程可以理解为主应用注册一个MicroAppStateActions并注册自己为监听者，子应用在props中获取到MicroAppStateActions并注册自己为监听者，主应用/子应用通过setGlobalState改变数据时，双方都可以触发监听事件；
第二种是redux的方案，基本思路是：主应用创建redux store（包含reducer和action的定义），在需要使用store的时候通过定义shared实例来实现（实例包含使用state和更新state），加载子应用时通过props传递shared实例给子应用，子应用接到shared实例后，使用其中的方法来获取和更新state
```
### 防抖就是执行一次函数之后，需要等待一段时间，在这段时间内再次触发，需要重置计时；而节流是执行一次函数后，需要冷却一段时间才会对下次触发做响应。
```javascript
    function debounce(fn,delay){
        let timer = null //借助闭包
        return function() {
            if(timer){
                clearTimeout(timer) //进入该分支语句，说明当前正在一个计时过程中，并且又触发了相同事件。所以要取消当前的计时，重新开始计时
                timer = setTimeout(fn,delay) 
            }else{
                timer = setTimeout(fn,delay) // 进入该分支说明当前并没有在计时，那么就开始一个计时
            }
        }
    }
    function debounce(fn,delay){
        let timer = null //借助闭包
        return function() {
            if(timer){
                clearTimeout(timer) 
            }
            timer = setTimeout(fn,delay) // 简化写法
        }
    }
```
### JavaScript的任务分为微任务（Microtasks）和宏任务（task）
```
宏任务是主流，当js开始被执行的时候，就是开启一个宏任务，在宏任务中执行一条一条的指令；
宏任务可以同时有多个，但会按顺序一个一个执行；
每一个宏任务，后面都可以跟一个微任务队列，如果微任务队列中有指令或方法，那么就会执行；如果没有，则开始执行下一个宏任务，直到所有的宏任务执行完为止，微任务相当于宏任务的小尾巴；
为什么有了宏任务，还会有微任务存在？因为宏任务太占用性能，当需要一些较早就准备好的方法，排在最后才执行的时候，又不想新增一个宏任务，那么就可以把这些方法，一个一个的放在微任务队列里面，在这个宏任务中的代码执行完后，就会执行微任务队列。
而Promise是微任务，setTimeout是宏任务
```
### 普通函数和箭头函数的区别：
```
1、箭头函数不可作为构造函数，不能使用new
2、箭头函数没有自己的this
3、箭头函数没有arguments对象
4、箭头函数没有原型对象
```