效果如下：
当扫图结束后，就会有黑色的罩层弹出
![在这里插入图片描述](https://img-blog.csdnimg.cn/c28858cb35004117bbb6d56e6a6ec470.png#pic_center)
如何实现：
1. 盒子里面放张花的背景图片，然后动态生成li标签，使用定位的方式，覆盖在花的上面
2. 当点击每一个li标签的时候，就触发一个事件，将盒子变成透明的，但是要注意触发了一次事件之后就需要将这个事件撤回，不然很耗性能
3. 如何判断游戏结束了呢？先在一个数组中存储扫完完整的花的坐标，每点击一次就去判断当前这个li的下标有没有在数组中，使用一个变量记录，当变量等于数组的长度，游戏结束

```javascript
  <style>
        body,ul,li{
            margin: 0;
            padding: 0;
        }
        ul li{
            list-style: none;
        }
        ul li{
            width: 50px;
            height: 50px;
            background-color: #ddd;
            position:absolute;
           
        }
        .container{
            width: 800px;
            /* height: 600px; */
            border: 2px solid #333;
            margin: 0px auto;
            text-align: center;
            position: relative;
        }
        .container h3{
            text-align:center;
        }
        .game{
            width: 800px;
            height: 600px;
            background-image: url(../images/games/flower.png);
            background-size: cover;
            background-color: khaki;
            position: relative;
        }
        .mask{
            width: 100%;
            height: 100%;
            background-color:rgba(0, 0, 0, 0.5);
            position:absolute;
            display: none;
            top: 0;
            left: 0;
        }
    </style>
<div class="container">
        <h3>扫图游戏</h3>
        <div class="game"></div>
        <div class="mask"></div>
    </div>
    <script>
    //生成ul
        var ul=document.createElement("ul");
        var game=document.querySelector(".game");
        var mask=document.querySelector(".mask");
        var data = [
      23, 24, 25, 38, 39, 40, 41, 42, 43, 53, 54, 55, 56, 57, 58, 59, 67, 68,
      69, 70, 71, 72, 73, 74, 75, 76, 77, 83, 84, 85, 86, 87, 88, 89, 91, 92,
      93, 99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 115, 116, 117, 118,
      119, 120, 121, 122, 123, 124, 130, 131, 132, 133, 134, 135, 136, 137, 138,
      139, 147, 148, 149, 150, 151, 152, 153, 166, 167, 168, 169,
    ];
        //动态生成li，使用定位的方式
        var sum=0;
        var len=0;
        for(var i=0;i<12;i++){
         for(var j=0;j<16;j++){
            
            var li=document.createElement("li");
            li.style.top=i*50+"px";
            li.style.left=j*50+"px";
            ul.appendChild(li);
            li.index=sum;
            sum++;
            li.onmouseover=function(){
                this.style.opacity =0;
                //找到在当前数组中的索引，如果没有找到，就返回-1
                var i=data.indexOf(this.index);
        
                if(i!==-1){
                    len++;
                    
                }
                //需要把事件清除掉，不然每一次点击就会触碰到事件
                this.onmouseover=null; 
                if(len==data.length){
                mask.style.display="block";
            }
            }
          
        }
    }
    game.appendChild(ul);
    </script>
```

