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