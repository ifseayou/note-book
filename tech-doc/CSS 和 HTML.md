# CSS 和 HTML 

## CSS

### `div`盒子模型

#### 实现一

~~~css
父元素：display:table
子元素：display:table-cell; 
	   vertical-align:middle /*该设置对本元素起作用，且本元素是行内元素*/
以上实现水平居中

子元素：margin:0 auto /*实现水平方向居中*/
~~~

**例子1：**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>vertical-align</title>
    <style type="text/css">
        * {
            padding: 0px;
            margin: 0px;
        }

        .warp1 {
            height: 80px;
            width: 100%;
            background-color: #14191e;
        }

        .warp1 h1 { /* 这个表示只有在wrap1下的h1，才是如下的样式*/
            color: #fff;
            text-align: center;
            line-height: 80px;
        }

        .warp2 {
            height: 400px;
            width: 100%;
            border: 1px #14191e solid; /*border是边界*/
            display: table;
        }

        /*对父元素设置display：table属性，对子元素设置table-cell，并对子元素设置vertiacl-align：middle*/
        .content {
            display: table-cell;
            vertical-align: middle; /*垂直方向上的对其方式，在中间*/
            /*vertical-align: middle只能实现对于行内元素的定位，使得本该元素是在垂直方向居中的，所以对其父元素设置table，并将该元素设置成为table-cell;
             content盒子的父元素是wrap1，这样就实现了两个p标签形成一个div，在父元素的盒子内形成垂直居中的效果，css的叠和效应和外边距形成一个水平垂直居中的效果
            */
        }

        .content div {
            width: 500px;
            font-family: "微软雅黑";
            margin: 0 auto; /*外边距是设置0和auto，能够实现盒子模型的水平居中*/
            line-height: 1.5em; /**设置行内高*/
            font-size: 14px;
        }

    </style>
</head>
<body>
<div class="warp1">
    <h1>欢迎来到慕课网</h1>
</div>
<div class="warp2">
    <div class="content">
        <div>慕课网，只学有用的！</div>
        <div>慕课网（IMOOC）是IT技能学习平台。你还可以和朋友一起编程。</div>
    </div>
</div>
</body>
</html>
```

**例子2：**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        /*补充代码*/
        div{
            background-color: #ececec;
            height: 150px;
            text-align: center;
            line-height: 150px;
            /*确定了背景颜色，高度，对于行内的单行元素实现了垂直方向和水平方向上的居中*/
        }
        .one{
            color: red;
            font-weight: bold;
            font-size: 80px;
        }
        .two{
            color: grey;
            letter-spacing: 2px;
            text-decoration: underline; /*文本的装饰为下划线*/
            vertical-align: 15px;
            /*设置位置上移相对于红色字体的上半部分， vertical-align: middle表示垂直放上上居中显示;*/
            font-size: 40px;

        }
        .three{
            font-size: 80px;
            color: grey;
            font-style: oblique;
            /*设置斜体*/
        }
    </style>
</head>
<body>
<div><span class="one">慕课网</span>&nbsp;&nbsp;<span class="two">只学有用的</span><span class="three">!</span></div>
</body>
</html>
~~~

**例子3**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            height: 150px;
            width: 100%;
            background-color: #eee;
            font-size: 2em;
            line-height: 150px;
            text-align: center;
        }
        .one{
            font-size: 2em;
        }
        .two{
            color: red;
            vertical-align: 10px;
            /*对于同行的元素实现了居中的样式*/
            text-decoration: underline;
        }
    </style>
</head>
<body>
<div>
    <img src=" http://climg.mukewang.com/59c21bae000157fa01000059.jpg">
    <span class="one">CSS层叠样式表</span>
    <span class="two">(Cascading Style Scheet)</span>
</div>
</body>
</html>
~~~

**例子4：一个例子理解div**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            height: 300px;
            width: 300px;
            /*background-color: transparent;/*设置背景颜色为透明*/
            background-color: red;
            margin: 20px;
            padding: 20px;
            border: 20px dashed ;
            /*如果没有设置边框的颜色，默认和元素内文本的颜色是一样的所以是黑色的。
             背景颜色是包含内边距和边框，但是不包含外边框的，所以
             此时，div的大小是380px；dashed 表示的是虚线。
            */
        }

    </style>
</head>
<body>
<div>背景颜色</div>
</body>
</html>
~~~

**例子，背景图片会占据`padding，boder，padding`**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            height: 300px;
            width: 300px;
            background-image: url(../img/bg3.jpg);
            /*背景图片的样式必须使用url的样式*/
            margin: 20px;
            padding: 20px;
            border: 20px dashed;
            /*关于元素的背景图片，元素的背景图片占据了元素的全部尺寸，包括内边距和边框，但是不包括外边距。
图片默认在浏览器的左上角，且在水平和垂直方向上是重复的,b背景图片会覆盖背景颜色，网站开发的时候，背景颜色也会设置，
防止图片出现问题的时候网站依然能够加载出来。*/
        }
    </style>
</head>
<body>
<div></div>
</body>
</html>
~~~

**居中的例子**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            height: 800px;
            width: 100%;
            background-image: url(http://climg.mukewang.com/58dc9d360001d65806500650.jpg);
            text-align: center;
            /*这表示的都是在本元素中的元素是居中的，水平居中*/
            display: table;
            font-size: 20px;
            line-height: 30px;
            /*background-repeat: no-repeat;*/
            /*该属性设置图片默认是重复的*/
            /*让某元素居中显示，设置在上一级别的元素中才可以 ，不能设置在本元素，比如此例中的text-align
            的作用是元素内的元素是居于水平中间的，且给元素是块级别的元素是设置，由于vertiacl-align属性只会对行内的元素有效，需要将其设置成为table表格的形式，并且需要把其子元素设置成单元格的模式，并且在其子元素上设置vertical-align属性，该属性只对行内元素有效。 */
        }
        span{
            display: table-cell;
            vertical-align: middle;
        }
    </style>
</head>
<body>
<div>
            <span>
              《长歌行》
              <br>青青园中葵，朝露待日晞。
              <br>常恐秋节至，焜黄华叶衰。
              <br>百川东到海，何时复西归。
              <br>少壮不努力，老大徒伤悲。
            </span>
</div>
</body>
</html>
~~~

**图片的位置**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            height: 2500px;
            width: 100%;
            background-image: url(../img/bg1.jpg);
            border: 1px solid red;

            background-repeat: no-repeat;
            background-attachment: fixed;
            /*background-attachment: scroll;*/
            background-position: center;
            /*background-position对于背景图片的定位，元素可以使用百分比，top bottom right left，（x，y），此时表示图片居中显示*/
            /*background-attachment: 属性设置图片的滚动方式，默认是随着滚动条的滚动，图片不发生滚动
              fixed表示随着滚动条的滚动，图片不滚动，使用fixed模式，即为固定的模式，广告图片的方式。
            */
        }

    </style>
</head>
<body>
<div>
</div>
</body>
</html>
~~~

**列表项的设计**

~~~HTML
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        ul li{
            /*list-style-type: none;*/
            /*列表项的标记,list-style-type:none/circle（空心圆）/disc(实心圆)/square(方块）*/
            /*list-style-type:circle; */
            list-style-type: decimal;
            /*有序的一些属性：decimal(从1开始的整数)/lower-roman(大写数字)/upper-roman(大写的罗马数字)/lower-alpha(小写的英文字母)
            /upper-alpha(大写的英文字母)*/
            /*list-style-image: url(../img/bg1.jpg);*/
            /*为列表添加图片的样式*/
            /*list-style-position: inside;*/
            /*list-style-position设置文本是不是会环绕图标，inside和outside*/

        }

    </style>
</head>
<body>
<ul>
    <li>家用电器</li>
    <li>冰箱</li>
    <li>洗衣机</li>
</ul>
</body>
</html>
~~~

**最小高度，最大宽度**

~~~html
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="UTF-8">
        <title>line-height属性</title>
        <style type="text/css">
      /*  *{
          padding: 0;
          margin: 0;
        }*/
             div{
                 width: 600px;
                 height: 600px;
                 background-color: pink;
             }
             p{
                 background-color: yellow;
             }
             .p1{
                 width: 30%;
                 height: 30%;
             }
             .p2{
                 max-height: 30%;
                 max-width: 30%;
             }
             .p3{
                 min-height: 200px;
                 /*即使没有那么大，也把它撑大一些*/
                 max-width: 300px;
                 /*东西再多，也得在这么大的范围内呆着*/
             }
/*最大，最小宽度高度：最小高度；不管元素有没有填满，元素最低的高度都是那么大，最大高度：若元素没有填满，元素祖东撑开高度，但是不会超过最大高度值
高度和宽度只能设置在块级元素上，和替换元素上，相关的替换元素有：<img><input><textarea>
*/
        </style>
    </head>
    <body>
     <div>
         <p class="p1">第一个P标签是父元宽高的30%</p>
         <p class="p2">第2个P标签的最大高度和最大宽度是父元的30%</p>
         <p class="p3">第3个P标签的最小高度是200px；最大宽度是300px</p>
     </div>
    </body>
</html>
~~~

**登录表单**

~~~HTML
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        div{
            width: 600px;
            height: 200px;
            border :1px solid  grey;
            font-size: 14px;
            /*设置盒子的CSS样式*/
        }
        p{
            width: 30%;
            font-size: 2em;
        }
        .text{
            width: 30%;
            height: 25%;
            /*设置文本框的CSS样式*/
        }
        .sub{
            width: 100px;
            height: 30px;
            background-color: orange;
            font-size: 20px;
            /*设置提交按钮的CSS样式*/
        }
        span{
            /*无法对行内元素设置宽度和高度*/
        }

    </style>
</head>
<body>
<div>
    <p>登录</p>
    <span>请输入您的信息：</span>
    <input type="text" name="" class="text">
    <input type="submit" name="" class="sub">
</div>
</body>
</html>
~~~

**`nth-child`**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        li{
            height: 50px;
            width: 200px;
        }
        li:nth-child(1){
            border-top: 2px solid red;
        }
        li:nth-child(2){
            border-right: 2px dotted green;
            /*dotted是设置边框为点状的*/
        }
        li:nth-child(3){
            border-bottom: 2px dashed blue;
        }
        li:nth-child(4){
            border-left: 2px solid purple;

        }
        li:nth-child(5){
            border:2px solid red;
        }

    </style>
</head>
<body>
<div>
    <ul>
        <li>第一个li</li>
        <li>第二个li</li>
        <li>第三个li</li>
        <li>第四个li</li>
        <li>第五个li</li>
    </ul>
</div>
</body>
</html>
~~~

**块元素、行内 元素之间的转换**

~~~HTML
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        .one {
            font-size: 0px;
        }

        /*将父元素设置成为0像素的时候，子元素也会继承，便能够消除边距，此时需要子元素设置font-size的大小*/
        div, span {
            background-color: #00aaee;
            border: 1px #666 solid;
        }

        div {
            display: inline-block;
            font-size: 16px;
            width: 100px;
            height: 30px;
            padding: 5px;
            margin: 10px;
        }

        /*inline-block属性表示既在行内显示，还可以像块级元素一样显示，所以可以像定义盒子一样来定义高宽*/
        span {
            display: block;
            width: 100px;
            height: 30px;
            padding: 10px;
            margin: 5px;
        }
    </style>
</head>
<body>

<div>display属性-div</div>
<div>display属性-div</div>
<div>display属性-div</div>
<div>display属性-div</div>
<div>display属性-div</div>
<div>display属性-div</div>

<hr>
    <span>display属性-inline</span>
    <span>display属性-inline</span>
    <span>display属性-inline</span>
<!-- 标签之前会有空格，原因是标签之间换行了，如果标签之间不换行，就没有空隙 -->
</body>
</html>
~~~

**鼠标悬停设置**

~~~HTML
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        span{
            display: none;
        }
        a:hover span{
            display: inline;
        }
        /*display可以有三个值得设置
        none表示不显示；block表示的是在块级显示；inline表示行内显示
        鼠标悬停的时候，会浮现内容。
        */

    </style>
</head>
<body>
<a href="#">指我，，，，<span>和你捉迷藏</span></a>
</body>
</html>

<!------------------------->
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        li{
            display: none;
        }
        /*初始状态，列表项是不显示的*/
        ul:hover li{
            display: inline;
        }
        /*当鼠标悬停在ul的时候，li标签显示，且在行内进行显示*/
    </style>
</head>
<body>


<ul>
    <h1>家电</h1>
    <!-- h1标签必须放在ul的标签内部，即在鼠标悬停设置标签的内部 -->
    <li>冰箱</li>
    <li>洗衣机</li>
    <li>空调</li>
</ul>

</body>
</html>

<!----------------------------->
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="UTF-8">
        <title>line-height属性</title>
        <style type="text/css">
            *{
                padding: 0;
                margin: 0;
            }
            div{
                /*width: 100px;*/
                /*height: 1000px;*/
                /*margin: 0 auto;*/
                /*background-color: #ececec;*/
                border:1px solid grey;
            }
            li{
                display: none;
                text-align: center;
                
            }
            ul:hover li{
                display: block;
            }
            h2{
                background-color: #ececec;
                border:1px solid grey;
                text-align: center;
                margin: 0;
            }
        </style>
    </head>
<body>
    <div >
        <ul>
            <h2>家电</h2>
            <li>冰箱</li>
            <li>洗衣机</li>
            <li>空调</li>
        </ul>
        <ul>
            <h2>洗护</h2>
            <li>洗衣液</li>
            <li>消毒液</li>
            <li>顺揉计</li>
        </ul>
        <ul>
            <h2>衣物</h2>
            <li>衬衣</li>
            <li>卫衣</li>
            <li>内衣</li>
        </ul>
    </div>
</body>
</html>

~~~

### 布局案例

**侧边栏**

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        .page{
            width: 100%;
            height: 4043px;
            background: url("../img/bg3.jpg") center top no-repeat;
        }
        .nav{
            /*导航的父容器*/
            width: 160px;
            height: 205px;
            position: fixed;

            left: 0;
            top: 50%;
            margin-top: -103px;
            /*此处实现导航的居中显示。值为本身值的一半，并且设置成为负数*/
            font-family: 'Miscrosoft Yahi';
        }
        .tit{
            width: 160px;
            height: 40px;
        }
        .nav-li{
            width: 160px;
            height: auto;
            border-bottom: 1px solid #fff;/**/
            /*fff表示白色*/
            background-color: #333;
            /*333表示的是黑色*/
            text-align: center;
            line-height: 40px;
            font-size: 16px;
            color: #fff;
            cursor: pointer;
            /*把把鼠标设置成为，当鼠标点击上时候，变成手的形状*/
        }
        .nav-li:hover ul{
            /*当鼠标移动到父元素上的时候，它的子元素li可以出现*/
            display: block;

        }
        .nav-li ul{
            width: 160px;
            height: auto;
            background-color: #fff;
            display: none;
            /*刚开始的时候，所有的li元素都应该是隐藏的*/
        }
        .nav-li ul li{
            width: 160px;
            height: 40px;
            border-bottom: 1px dashed #666;
            /*浅灰色*/
            color: #333;
            text-align: center;
            line-height: 40px;
            position: relative;
            /*父元素设置成为relative，能有效的控制子元素绝对定位的位置*/
        }
        .nav-li ul li:hover .list-3{
            display: block;
        }
        .list-3{
            width: 160px;
            height: auto;
            position: absolute;
            left: 160px;
            top: 0;
            display: none;
        }
        .list-3Dom{
            width: 160px;
            height: 40px;
            background: #444;
            border-bottom: 1px solid #fff;
            text-align: center;
            line-height: 40px;
            color: #fff;
        }

    </style>
</head>
<body>
<div class="page">
    <div class="nav">
        <div class="nav-li">
            <div class="tit">慕课网标题</div>
            <ul>
                <li>
                    二级栏目
                    <div class="list-3">
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                    </div>
                </li>
                <li>
                    二级栏目
                    <div class="list-3">
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                    </div></li>
                <li>
                    二级栏目
                    <div class="list-3">
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                        <div class="list-3Dom">三级栏目</div>
                    </div></li>
            </ul>
        </div>
        <div class="nav-li">慕课网标题
            <ul>
                <li>二级栏目</li>
                <li>二级栏目</li>
                <li>二级栏目</li>
            </ul>
        </div>
        <div class="nav-li">慕课网标题
            <ul>
                <li>二级栏目</li>
                <li>二级栏目</li>
                <li>二级栏目</li>
            </ul>
        </div>
        <div class="nav-li">慕课网标题
            <ul>
                <li>二级栏目</li>
                <li>二级栏目</li>
                <li>二级栏目</li>
            </ul></div>
        <div class="nav-li">慕课网标题
            <ul>
                <li>二级栏目</li>
                <li>二级栏目</li>
                <li>二级栏目</li>
            </ul>
        </div>

    </div>
</div>
</body>
</html>
~~~

**侧边栏广告**

~~~HTML

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
    <style>
        /*我们学习了CSS中的定位，那么现在我们来运用定位的知识来实现网页中常见的对联广告吧。

      要求：无论页面怎么移动，页面左右的的广告栏不会发生位置的变化*/
        *{
            padding: 0;
            margin: 0;
        }
        .one{
            width: 100%;
            height: 4043px;
            background: url("http://climg.mukewang.com/59c9f7ce0001839219034033.png") center top no-repeat;
        }
        .left{
            position: fixed;
            left: 0;
            top: 50%;
            margin-top: -150px;
            width: 200px;
            height: 300px;
            background-image: url("http://climg.mukewang.com/5a3383c70001f1b702240364.png");
        }
        .right{
            position: fixed;
            right: 0;
            top: 50%;
            margin-top: -150px;
            width: 200px;
            height: 300px;
            background-image: url("http://climg.mukewang.com/5a3383d00001a3dd02240364.png");
        }
    </style>
</head>
<body>
<div class="one">
    <div class="left"></div>
    <div class="right"></div>
</div>
</body>
</html>
~~~

#### 一个常用的结构--栏目居中

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
  <title></title>
  <style>
     *{
      margin: 0;
      padding: 0;
      color: #fff;
      /*这是白色*/
      text-align: center;
     }
    .one{
      height: 1000px;
      width: 800px;
      background-color: #4c77f2;
      /*这个是蓝色的*/
      margin: 0 auto;
      /*块居中的方式，外边距上下为零，左右自动*/

    }
  </style>
</head>
<body>
  <div class="one">这个是页面的内容</div>
</body>
</html>
~~~

#### 常用结构--块上下左右居中

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
    <style>
        /*行布局自适应：*/
        *{
            margin: 0;
            padding: 0;
            color: #fff;
            /*这是白色*/
            text-align: center;
        }
        .one{
            height: 300px;
            width: 800px;
            background-color: #4c77f2;
            position: absolute;
            top:50%;
            left: 50%;
            margin-left: -400px;
            margin-top: -150px;
            /*居中且垂直的方式。是非常有效的方法*/
        }
    </style>
</head>
<body>
<div class="one">这个是页面的内容</div>
</body>
</html>

<!--++++++++++++++++++-->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title></title>
    <style type="text/css">
        /*此处写代码*/
        *{
            padding: 0;
            margin: 0;
            text-align: center;
        }
        .container{
            width:1100px;
            height:100px;
            background-color: black;
            position: absolute;
            left: 50%;
            top: 50%;
            margin-top: -50px;
            margin-left: -550px;
        }
        img{
            height: 100px;
            width: 200px;
            position: absolute;
            left: 0;
            cursor: pointer;
        }
        .two{
            height: 100px;
            width: 500px;
            color: white;
            font-size: 20px;
            text-align: center;
            line-height: 100px;
            position: absolute;
            right: 0;
            cursor: pointer;
        }
    </style>
</head>
<body>
<!-- 此处写代码 -->
<div class="container">
    <img src="http://climg.mukewang.com/58c0d2d900016ce303000100.png">
    <div class="two">课程&nbsp;&nbsp;&nbsp;职业路径&nbsp;&nbsp;&nbsp; 实战&nbsp;&nbsp;&nbsp; 猿问&nbsp;&nbsp;&nbsp; 手记</div>
</div>
</body>
</html>
~~~

#### 网页常用布局

~~~HTML
<!DOCTYPE html>
<html>
<head>
  <!-- 本例为多行布局，网页经常的结构-->
  <meta charset="UTF-8">
  <title></title>
  <style type="text/css">
    /*行布局固定宽，行布局自适应，行布局导航随屏幕滚动*/
    *{
      padding: 0;
      margin: 0;
      text-align: center;
      /*这是只是设置文本的对其为居中对齐，并不能使得元素内部的其他元素是居中整齐的*/
      font-size: 16px;
      color: #fff;
      /*字体的颜色为黑色*/
    }
    .header{
      width: 100%;
      height: 50px;
      background-color: #333;
      /*灰色*/
      margin: 0 auto;
      line-height: 50px;
      position: fixed;
      /*设置了position之后，会发现 margin: 0 auto，在宽度为800px的情况下不在能够做到居中的效果。*/
    }
    .banner{
      width: 800px;
      height: 300px;
      background-color: #30a457;
      margin: 0 auto;
      padding-top: 50px;
    }
    .container{
      width: 800px;
      height: 1000px;
      background-color: #4c77f2;
      margin: 0 auto;

    }
    .footer{
      width: 800px;
      height: 100px;
      background-color: #333;
      margin: 0 auto;
      line-height: 100px;
    }
   
  </style>
</head>
<body>
  <!-- 此处写代码 -->
  <div class="header">这是页面的头部</div>
  <div class="banner">这是页面的banner图</div>
  <div class="container">这是页面的内容</div>
  <div class="footer">这是页面的底部</div>
</body>
</html>
~~~

### float浮动

**例子1**

~~~html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>line-height属性</title>
    <style type="text/css">
        *{
            padding: 0;
            margin: 0;
        }
        .one{
            float: left;
        }
        .test{
            width: 100px;
            height: 100px;
            background-color: red;
            float: inherit;

            /*设置了浮动属性之后，元素会进行浮动，往左或者是往右，脱离原来的位置，float的值有四个：left，right，none，inherit
            inherit继承父元素的浮动属性进行浮动。*/
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="one">
    <div class="test">1</div>
    <div class="test">2</div>
</div>
</body>
</html>
~~~

**例子2**

~~~html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        *{
            padding: 0px;
            margin: 0px;
        }
        .div1{
            width: 20px;
            height: 100px;
            float:left;
            background: red;
        }
        .div2{
            width: 100px;
            height: 300px;
            float:right;
            background: blue;
            /**/
        }
        .div3{
            width: 30px;
            height: 100px;
            float:inherit;
            background: green;
            /*继承div2的浮动属性，进行右浮动*/
        }
    </style>
</head>
<body>
<div class="div1"></div>
<div class= "div2">
    <div class="div3"></div>
</div>
</body>
</html>
~~~

**文字环绕**

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        body{
            font-family: '微软雅黑';
        }
        .per{
            width: 400px;
            height: 200px;
            border: 1px solid #CCC;
        }
        .red{
            width: 100px;
            height: 100px;
            background: red;
            float:left; /* 如果没有浮动，red盒子将会独占一行，如今有了浮动，div将会脱离原来的位置，其他元素将会填充过来*/ 
            margin: 10px;
        }
    </style>
</head>
<body>
<div class="per">
    <div class="red"></div>
    慕课网是垂直的互联网IT技能免费学习网站。以独家视频教程、在线编程工具、学习计划、问答社区为核心特色。在这里，你可以找到最好的互联网技术牛人，也可以通过免费的在线公开视频课程学习国内领先的互联网IT技术。慕课网课程涵盖前端开发、PHP、Html5、Android、iOS、Swift等IT前沿技术语言，包括基...
</div>
</body>
</html>
~~~

**坍塌：父元素高度auto，子元素float之后造成的坍塌**

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        .per1{
            width: 500px;
            height: auto;
            border: 1px solid #000;
        }
        .one{
            height: 100px;
            width: 100px;
            background-color: red;
            border: 1px solid #fff;
            float: left;
            /*float: left;添加之后，使得元素脱离了正常的标准刘，父元素发生塌陷的现象，没有了高度。*/
        }
        .per2{
            width: 100px;
            height: 200px;
            background-color: blue;
        }
        /*添加了兄弟元素，但是这个兄弟元素没有出现在他的兄弟的下方而是直接填补了塌陷的位置。*/

    </style>
</head>
<body>
<div class="per1">
    <div class="one"></div>
    <div class="one"></div>
    <div class="one"></div>
    <div class="one"></div>
</div>
<div class="per2"></div>
</body>
</html>

<!--坍塌的解决办法1：给父元素手动添加高度-->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        .per {
            width: 500px;
            height: 32px;
            /*手动给父元素添加高度就是解决塌陷的第一个方法，但是这个方法有一定的弊端，就是当子元素的个数不确定的时候，还是可能会发生塌陷，比如这个时候把子元素的个数增加到大于父元素的宽度的时候*/
            border: 1px solid #000;

        }
        .one {
            height: 30px;
            width: 100px;
            background-color: red;
            border: 1px solid #fff;
            float: left;
            /*float: left;添加之后，使得元素脱离了正常的标准刘，父元素发生塌陷的现象，没有了高度。*/
        }
    </style>
</head>
<body>
<div class="per">
    <div class="one"></div>
    <div class="one"></div>
    <div class="one"></div>
</div>
</body>
</html>

##坍塌解决的办法2：
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        .per{
            height:auto;
            width: 500px;
            border: 1px solid #000;
            overflow: hidden;
            zoom:1;
            /*overflow主要用来解决溢出的问题，但是会使得我们的元素被截取掉一部分元素*/
        }
        .one{
            width: 100px;
            height: 30px;
            background-color: red;
            border: 1px solid #fff;
            float: left;
            /*此时，元素发生塌陷，解决的办法是，在父元素上添加overflow和zoom属性*/
        }
    </style>
</head>
<body>
<div class="per">
    <div class="one"></div>
    <div class="one"></div>
    <div class="one"></div>
    </div>
</body>
</html>
~~~

**如果一个盒子设置了浮动：**

* 如果该盒子没有设置浮动，那么该盒子的兄弟元素将会占领浮动元素的位置，该兄弟元素可以清除前任兄弟留下来的浮动，该兄弟元素就能够到达正常的位置
* 如果该盒子设置了浮动，本来应该去占领兄弟的位置，但是由于此时自己也浮动了，所以再次脱离自己的位置，挨着兄弟的边进行浮动

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        .per{
            height: 200px;
            width: 400px;
            border: 1px solid #ccc;
        }
        .one{
            width: 100px;
            height: 100px;
            background-color: red;
            float: right;
            /*此处设置浮动会使得后来的元素的位置发生变化，使得这个元素脱离原来的文本流，和一般的区块不在一个布局流中。
            进行左右的浮动，导致后来的元素发生塌陷的现象，解决的办法是在后来的元素上也加上左右的浮动*/
        }
        .two{
            width: 120px;
            height: 120px;
            background-color: blue;
            /*float: left;*/
            clear: right;
            /*添加清除浮动之后，不允许左边有浮动的出现，很好的解决了元素塌陷的问题*/
            /*当设置为清除右边浮动的时候，此时不能产生右边的浮动*/
        }
        /*.thr{
            width: 150px;
            height: 150px;
            background-color: yellow;
        }*/

    </style>
</head>
<body>
<div class="per">
    <div class="one"></div>
    <div class="two"></div>
    <!--     <div class="thr"></div> -->
    </div>
</body>
</html>
~~~









### CSS标识

~~~css
text-indent: 2em;	/*设置首行文字缩进*/


line-height: 1.5em;
/* 设置两行之间的高度差 ，line-height属性用于设置多行元素的空间量，如多行文本的间距。对于块级元素，它指定元素行盒（line boxes）的最小高度。对于非替代的 inline 元素，它用于计算行盒（line box）的高度。
*/

~~~





## 资源链接：

[前后端分离](http://taobaofed.org/blog/2014/04/05/practice-of-separation-of-front-end-from-back-end/)：**文章中简要记录的要点**

- 前端：负责 View 和 Controller 层。
- 后端：只负责 Model 层，业务处理/数据等

