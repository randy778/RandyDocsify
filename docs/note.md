### webpack loader配置从右往左执行
### dom中quereySelector和getElementByID的区别：静态获取和动态获取
### js事件循环规律可大致可以参考如下两套代码搞清楚：
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