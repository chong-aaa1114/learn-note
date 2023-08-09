call方法的作用
运用:函数.call(thisArg,arg1,arg2,……）
> 改变this指向，将this指向thisArg

现在有一个对象以及一个sum函数

```javascript
var obj={
	a:1,
	b:2
}
function sum(a,b){
	console.log(this.a,this.b);
}
```
如果调用了call函数,此时this指向的是obj，所以输出1，2

```javascript
sum.call(obj,4,5)//输出1，2
```
手写call方法，需要改变this指向和参数的传递
this指向
==context:上下文对象，也就是上面的obj==
```javascript
Function.prototype._call=function(context,...args){
	console.log(this);//此时的this指向的是sum函数，因为是sum函数调用的_call方法
	//改变this指向,需要在对象中新建一个属性来接收这个this
	var key='k';
	context[key]=this;//context中的key属性值就是sum函数，改变this指向完毕
	//参数问题
	//context['key']()（括号需要传入参数，相当于在sum函数中传入参数，但是参数的个数并不确定)
	//先使用es6的剩余参数来解决
	var result=context[key](...args);
	//不能改变对象原来的样子，所以需要删除掉新增的属性
	delete context[key];
	return result;
}
sum._call(obj,4,5)//输出的是1，2，因为this指向的是obj对象
```
`context['key'](...args);`相当于

```javascript
var obj={
	a:1,
	b:2,
	key:function sum(a,b){
	console.log(this.a,this.b);
	}
}
```
此时问题就解决了，但是如果面试官突然说不能使用es6中的剩余参数，使用es5的方法呢
==这时需要动态生成函数了==
首先，需要思考我们需要生成什么样的函数呢
动态：参数的个数不确定
```javascript
 context[key](arg[1],arg[2],arg[3]);//需要生成类似这样的函数
```
怎么生成一个函数呢？==Function==

```javascript
var fn = new Function(arg1,arg2,arg3...,functionBody)
```
arg1,arg2,arg3是参数，functionBody是函数体代码，参数也可以不写，就相当于是没有传入参数的函数
```javascript
var sum= new Function("a","b","consloe.log(a+b)")
这个函数就相当于
function anonymous(a,b){
	consloe.log(a+b)
}
```
所以我需要构造一个函数，然后函数的返回值应该是
`return new Function(传入的参数，函数体代码)`,函数体代码就是`return context[key](动态参数）`

```javascript
function createFn(arrLength){
	var code="return context[key](";
	for(var i=0;i<arrLength;i++){
	//第二个就需要加上逗号
	if(i>0){
		code+=",";
	}
	code+="arr["+i+"]";
	}
	code+=")";
	return new Function("context","key","arr",code);
}
```
在上面的函数中，传入的参数是一个数组的长度，需要将一开始传进来的参数（arguments）装在数组中，然后获取到长度

```javascript
Function.prototype._call=function(context){
//需要过滤掉第一个参数，所以需要从1开始
var arr=[];
for(var i=1;i<arguments.length;i++){
	arr.push(arguments[i]);
}
var len=arr.length;
//var fn=createFn(len);
//返回的函数是一个函数，Function函数new出来的函数
//function anonymous(context,key,arr){
//	return context[key](arr[0],arr[1]);
//}
//fn(context,key,arr);
var result=context[key](len)(context,key,arr);
delete context[key];
return result;
```
==context参数的情况处理：==

 - 传进来是一个null，需要改变this指向，（globalThis在浏览器中指向的是wndow,在nodejs中指的是global），需要context={}
 - context传进来的不是一个对象，而是一个基本数据类型，需要转换为一个对象
 - 如果调用call方法 不是一个函数，而是一个对象,下面说明


```javascript
var obj = Object.create(Function.prototype);
// obj是一个对象，但他能调用call方法
```

```javascript
if(context==null) context=globalThis
if(typeof context!==Object) context=new Object(context)
if(typeof this!=function){
throw new TypeError("Type Error");
}

```
==对象中新增属性key==
新增的属性有可能会和原来的相同，需要使用随机函数

```javascript
//判断这个属性是否在对象身上有的
var key="fn"+Math.random()*new Date().getTime();
while(context hasOwnproperty(key)){
 key="fn"+Math.random()*new Date().getTime();
}
```
贴上完整代码：

```javascript
  <script>
        Function.prototype._call=function(context,...args){
            console.log(this);//调用call方法的是sum函数，所以this指向的是sum函数
            //如果传进来的是一个对象，但是这个对象是以Function.prototype为原型，也就是说可以调用函数上的call方法
            if(typeof this!=="function") {
                throw new TypeError("Type Error");
            }
            if(typeof context!==Object)  context=new Object(context);
            //如果传的第一个参数是null，那么内部的this指向的是window，在浏览器中是window，在nodejs中是global
            if(context==null) context=globalThis;//变成一个{}，空对象
            console.log(context);
            console.log(globalThis);
            var key='fn_'+Math.random()*new Date().getTime();
            while(context.hasOwnProperty(key)){
                key='fn_'+Math.random()*new Date().getTime();
            }
            //给context添加一个属性，指向调用call方法的函数
            context[key]=this;
            //函数需要有参数传入,有一个值来接收，因为需要删除掉新加的属性
            var result=context[key](...args);
            context[key](...args);
        
        
            delete context[key];
            return result;
        }
        function sum(a,b,c){
            console.log(this.a,this.b,this.c);
            // console.log(a,b,c);
        } 
        var obj={
            a:1,
            b:2
        }
        sum._call(obj,5,6);//输出1，2，undefined
```

