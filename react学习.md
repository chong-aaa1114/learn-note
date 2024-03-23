# 怎么快速学习react

在稀土掘金看到了这篇文章，觉得挺有用的

[快速上手react](https://juejin.cn/post/7268844150233219107)

==反思：之前的自己肯定会想要把语法都学一遍，再上手项目，但是在时间不多的情况下，这样子没有什么效果，要对比vue和react，把一些经常需要用到的结合做项目来学习。有个网站可以对比vue和react的语法：[vue和react语法对比](https://component-party.jason-liang.com/)==

## 一些常见的功能

![image-20240323002231425](../images/image-20240323002231425.png)

## 一些经验教训

![image-20240323002254553](../images/image-20240323002254553.png)![image-20240323002305640](../images/image-20240323002305640.png)

## react相关文档

[文档1](https://react.dev/learn)

[文档2](https://react.docschina.org/learn)

## 如何上手

首先找一些初学者的文档对react有一个初步的了解，包括一些学习的资料

需要知道的知识

### react的工作原理：（待纠正）

![image-20240323002344200](../images/image-20240323002344200.png)

### 相关js库

> 1. `react.js`：React核心库。
> 2. `react-dom.js`：提供操作DOM的react扩展库。
> 3. `babel.min.js`：解析JSX语法代码转为JS代码的库。

### 创建项目

> 1. 安装create-react-app：npm install -g create-react-app
> 2. 检查是否安装成功：create-react-app -V // 注意：V 是大写的 
> 3. 创建项目：npx create-react-app  myapp（myapp是项目名字）

![image-20240323002401741](../images/image-20240323002401741.png)

![image-20240323002417108](../images/image-20240323002417108.png)

之后就跟着codewhy的视频做

![image-20240323002424477](../images/image-20240323002424477.png)

>  jsconfig.json文件的作用：可以提供智能提示

1. 给项目配置别名（之后在引入的时候就会很方便）

   > 将@=>src(这是属于webpack的配置)，但是有个问题，react脚手架隐藏webpack
   >
   > 解决一：npm run eject(不推荐)
   >
   > 解决二：craco=>create-react-app config(推荐)
   >
   > > - 安装：npm install @craco/craco@alpha -D
   > > - 创建`craco.config.js`文件，这文件是node加载的，通过commonjs导出一个对象
   > >
   > > ![image-20240323002435386](../images/image-20240323002435386.png)
   > >
   > > - 修改package.json文件，改成craco
   > >
   > >   ![image-20240323002441679](../images/image-20240323002441679.png)

2. 关于less（官网已经作了修改，后面有用到再去看）

   > npm i -D craco-less --force
   >
   > 在文件craco.config.js中添加配置：
   >
   > ![image-20240323002449421](../images/image-20240323002449421.png)

   提示：只要修改了webpack，都需要重新启动项目

## react知识点

![image-20240323002457557](../images/image-20240323002457557.png)

改变值：react如果是改掉原值，就需要使用到setName方法，如果赋给另一个变量，可以直接赋值（计算属性）

![image-20240323002507241](../images/image-20240323002507241.png)

# 解决问题的方法

- 去Google上面搜索
- 去stackflowover查找
- 去bing上面查

