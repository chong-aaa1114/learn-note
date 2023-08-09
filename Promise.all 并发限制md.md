# Promise.all 并发限制 同时只能有5个请求

什么叫做根源上就是控制promise的实例化

```js
poolLimit:并发数的限制
array：
iteratorFn：promise对象
function asyncPool(poolLimit, array, iteratorFn) {
    let i = 0;
    const ret = [];//已经完成的异步操作数组
    const executing = [];//正在执行的异步操作数组
    const enqueue = function () {
        // 边界处理，array为空数组
        if (i === array.length) {
            return Promise.resolve();
        }
        // 每调一次enqueue，初始化一个promise
        const item = array[i++];
        const p = Promise.resolve().then(() => iteratorFn(item, array));
        // 放入promises数组
        ret.push(p);
        // promise执行完毕，从executing数组中删除
        const e = p.then(() => executing.splice(executing.indexOf(e), 1));
        // 插入executing数字，表示正在执行的promise
        executing.push(e);
        // 使用Promise.rece，每当executing数组中promise数量低于poolLimit，就实例化新的promise并执行
        let r = Promise.resolve();
        if (executing.length >= poolLimit) {
            r = Promise.race(executing);
        }
        // 递归，直到遍历完array
        return r.then(() => enqueue());
    };
    return enqueue().then(() => Promise.all(ret));
}
```

promise.all方法会并发执行数组中所有的promise，现在想要控制在一定的时间内只能执行一定数量的promise

![ ](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230609163958718.png)

![image-20230609164022163](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230609164022163.png)

 谁先输出，取决于谁的状态先改变（then方法能够执行，就是因为状态改变了）

如果在add方法中传入的不是一个函数，而是立即把timeout方法执行了，那么就是一起在等待状态改变(并发操作)，时间最短，谁的状态就先改变，输出顺序是3 4 2 1

如果add方法中传入的是一个函数，那么这个函数的调用是在for循环中执行的，也就是顺序执行（不是并发操作）

分析如下：

> (1000,1)先传入，会放在延时队列中等待promise状态的改变，主线程会继续执行for循环，此时调用的是（500，2）的函数，也是进入延时队列，for循环有个数限制，此时不会再调用其他函数
>
> 500ms时间到了，（500，2）这个函数的状态改变了（时间一到，就调用resovle方法），成功的promise，那么就会执行timeout(time).then(())的then方法，输出数字2，此时有1个空位，那么函数（300，3）就会被调用，其他的暂时不能调用
>
> 300ms时间到了，（300,3）这个函数的状态改变，成功的promise，那么就会执行timeout(time).then(())的then方法，输出数字3,此时有一个空位，那么函数（400，4）会被调用
>
> 1000ms到了（前面累加），（1000，1）这个函数的状态就改变，成功的promse，那么就会执行timeout(time).then(())的then方法,输出数字1，此时有一个空位，但是没有别的函数需要再调用了
>
> 1200ms到了，（400，4)这个函数的状态就改变了，成功的promise，那么就会执行timeout(time).then(())的then方法，输出数字4
>
> 综上所述：输出顺序为2 3  1 4 

```js
class Scheduler{
    list=[]
    maxNum=2
    workingNum=0
    add(promiseCreator){
        this.list.push(promiseCreator)
}
    start(){
        for(var i=0;i<this.maxNum;i++){
            this.doNext();
        }
}
    doNext(){
        if(this.list.length&&this.workingNum<this.maxNum){
            this.workingNUm++;
            this.list.shift()().then(()=>{
                this.workingNum--;
                doNext()
            })
}
}
}
const timeout=time=>new Promise(resolve=>setTimeout(resolve,time))
const scheduler=new Scheduler()
const addTask=(timer,order)=>{
    scheduler.add(()=>timeout(time).then(()=>console.log(order)))
}
addTask(1000,1)
addTask(500,2)
addTask(300,3)
addTask(400,4)
scheduler.start()
```

限制并发请求,占用通道和释放通道

什么时候占用通道（for循环执行第一个的时候）

什么时候释放通道（文件切片上传成功之后）

前端文件上传遇到的问题：

- 为什么在上传的时候，切片数组前面的状态没有变成done,还是uploading呢

  (使用闭包解决这个问题就可以了)

  ![image-20230615153102566](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230615153102566.png)

![image-20230615155544683](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230615155544683.png)

为什么先执行的微任务是3，而不是1呢（微任务先进去的先执行，这里执行的不一定是3，只是这里的索引为3而已）

![image-20230615170133161](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230615170133161.png)



![image-20230617151323404](C:\Users\马镇涛\AppData\Roaming\Typora\typora-user-images\image-20230617151323404.png)