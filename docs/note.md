### webpack loader配置从右往左执行
### dom中quereySelector和getElementByID的区别：静态获取和动态获取
### js事件循环规律可大致总结为如下：
* js中有三个任务队列：主任务队列，job queue，message queue；
* 它们的优先级是：主任务队列 > job queue > message queue；
* 每当要执行下一个任务前（或者一个任务完成后），js会根据优先级询问各个任务队列是否为空，一旦遇到非空任务队列时则取其第一个任务执行。
* 具体可以参考：https://blog.csdn.net/black_general/article/details/83783972
### setState本身并不是异步，只是因为react的性能优化机制体现为异步。在react的生命周期函数或者作用域下为异步，在原生的环境下为同步
### 除了qiankun，阿里系的还有icestark和Alfa的微前端框架，可以了解下看看区别