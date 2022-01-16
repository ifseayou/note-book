# jQuery

jQuery是JS的一个函数库，装了***dom、bom***，Js 查询。

区别两种js库库文件：

* 开发版本、测试版本
* 生产版本（压缩版）

核心的概念

* **jQuery核心函数： ` jQuery 、 $`**

  ~~~javascript
  console.log($) // 返回的是一个函数
  console.log(typeof $) // function
  ~~~

* **jQuery对象：执行$()返回的对象**

  ~~~javascript
  var  $jQvar =$();// 有特别的变量命名规则
  console.log($() instanceof Object);
  ~~~

**例子：**

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>jQuery学习</title>
     <!-- 使用原生的dom -->
    <script type="text/javascript">
        window.onload = function() {
            var but1 = document.getElementById('but1');
            but1.onclick = function(){
                var value = document.getElementById('username').value;
                alert(value);
            };
        };
    </script>
    
     <!-- 使用jQuery,在此之前需要引入js库 -->
    <!-- <script type="text/javascript" src="js/jquery-3.1.0.js"></script> -->
    <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.js"></script>
    <script type="text/javascript">
        // 绑定文档加载完成的监听，语法上的意思是执行一个函数，该函数的参数也是一个函数
        $(function(){
            $('#but2').click(function(){
                var username = $('#username').val();
                alert(username);
            })
        })
    </script>
    
</head>

<body>
    <input type="text" id="username">
    <button id="but1">原生</button>
    <button id="but2">jQuery</button>
</body>
</html>
~~~

## $

* 作为函数使用：**$(param)**

  * 参数为函数：当DOM加载完成之后，执行此函数
  * 参数为选择器字符串：查找所有匹配的标签，并将他们封装为jQuery对象
  * 参数为DOM对象：将DOM对象封装为jQuery对象
  * 参数为HTML标签：创建标签对象，并封装为jQuery对象

  包装成***jQuery***对象的好处是可以使用jQuery对象的一些方法

* 作为对象使用：**$.xx()** 

  * $.each() 隐式遍历数组
  * $.trim() 去除两端的空格

**例子：**

~~~HTML
<div>
    <button id="but1">测试</button><br>
    <input type="text" name="m1" id="t1"><br>
    <input type="text" name="m2" id="t2"><br>
</div>
~~~

~~~JavaScript
<script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.js"></script>
<script>
    $(function(){
        $('#but1').click(function(){ // 这里的click传入一个回调函数，表示在完成了点击之后，执行回调函数
            // this:发生事件的dom元素，也即button
            //alert(this.innerHTML);

            // 此时参数为dom对象，将其封装为jQuery对象
            alert($(this).html());
            
            // 参数是html标签
            $('<input type="text" name="m3" id="t3"><br>').appendTo('div');
        }); 
    });

    var arr = [2,3,4,5];
    $.each(arr,function(index,itme){
        console.log(index + ":" + itme);
    });

    var str = "  i  miss you so much ..  ";
    console.log($.trim(str));
</script>
~~~

## jQuery 核心对象

jQuery核心对象：也就是 

* 执行jQuery核心函数返回的对象，
* jQuery对象内部包含的是dom元素对象的伪数组（可能只有一个）
* jQuery对象拥有很多的属性和方法，程序员可以方便的操作DOM

**例子：**

~~~JavaScript
<button >test1</button>
<button >test2</button>
<button id="bit3">test3</button>
<button >test4</button>
<script>
var $buttons = $('button');

// 获取有多少个按钮
console.log($buttons.length);// 4

// 查看第二个button的元素内容
console.log($buttons[1].innerHTML); // test2

// 打印所有的button的内容
$buttons.each(function (index, item) {
    console.log(index +":" + item.innerHTML);
});
/**
   0:test1
  1:test2
  2:test3
  3:test4
 */

// 输出‘test3’按钮是所有按钮中的第几个？
console.log($('#bit3').index()); // 2
~~~



**`<span title = "hello">` 其中的 title属性，当鼠标放在上面的时候，会出现提出”hello“**

## 选择器

**$("这里面放置的就是选择器")** 有下面的这几类：

* 基本选择器：查找类选择器，id选择器等
* 层次选择器：是用来查找子孙后代的
* 过滤选择器，少用
* 表单选择器，少用



### 基本选择器

~~~html
<body>
    <div id="div1" class="box">div1</div>
    <div id="div2" class="box">div2</div>
    <div id="div3" >div3</div>
    <span class="box">span(class = "box")</span>

    <br>

    <ul>
        <li>AAAAA</li>
        <li title="hello">BBBBB(tilte="hello")</li>
        <li class="box">CCCCC</li>
        <li title="hello">DDDDD(title="hello")</li>
    </ul>
 <script typeof="text/javascript">
     // 选择div1，并将div1改为红色
    $('#div1').css({'background':'red'});

    // 选择所有的div元素
     $('div').css({'font-size':'20px'});

     // 选择所有的class属性为box的元素
     $('.box').css({'background':'blue'});

     // 选择所有的div和span的元素
     $('div,span').css({'background':'pink'});

     // 选择所有class属性为box的div元素  . 表示的是交集
     $('div.box').css({'background':'yellow'})
 </script>
</body>
~~~

![1563373634445](img/jq/1.png) 

### 层级选择器

~~~html
<body>
    <ul>
        <li>AAAAA</li>
        <li class="box">CCCCC</li>
        <li title="hello"><span>BBBBB</span></li>
        <li title="hello"><span>DDDDD</span></li>
        <span>EEEEE</span>
    </ul>
 <script typeof="text/javascript">
    // 选中ul下面的所有span；  a  b  在a祖先元素下所有的后代元素
    $('ul span').css({'background':'yellow'})

     // 选中ul下面所有的子元素span ； a > b 在给定父元素的情况下匹配所有子元素
    $('ul > span').css({'background':'pink'});

     // 选中class为box的下一个li  ；a+b 匹配紧接在a元素后面的b元素
    $('.box + li').css({'background':'red'});

     // 选中ul下的class为box的元素后面的所有兄弟元素 ：a~b 匹配所有a元素之后的所有b元素
    $('ul .box~* ').css({'background':'black'});
 </script>
</body>
~~~

## AJAX

AJAX的实际意义是，不发生页面跳转、异步载入内容并改写页面内容的技术。简单的理解为通过JS向服务器发送请求

**同步**

JAX出现之前，我们访问互联网时一般都是同步请求，也就是当我们通过一个页面向服务器发送一个请求时，在服务器响应结束之前，我们的整个页面是不能操作的，也就是直观上来看他是卡主不动的

**异步**

通过AJAX向服务器发送请求，发送请求的过程中我们浏览网页的行为并不会收到任何影响，甚至主观上感知不到在向服务器发送请求。

**HTTP请求**

| 请求首行 |
| -------- |
| 请求头   |
| 空行     |
| 请求体   |

这是一个请求报文的格式，那我们如果手动的创建这么一个报文格式来发送给服务器想必是非常麻烦呢，于是浏览器为我们提供了一个`XMLHttpRequest`对象， XMLHttpRequest对象用来封装请求报文，我们向服务器发送的请求信息全部都需要封装到该对象中。

### Java中操作Json

在Java中可以从文件中读取JSON字符串，也可以是客户端发送的JSON字符串。 首先解析JSON字符串我们需要导入第三方的工具，目前主流的解析JSON的工具大概有三种json-lib、jackson、gson，三种解析工具相比较json-lib的使用复杂，且效率较差。而Jackson和gson解析效率较高。使用简单，这里我们以**gson**为例讲解。

n  Gson是Google公司出品的解析JSON工具，使用简单，解析性能好。

Gson

中解析

JSON

的核心是

Gson

的类，解析操作都是通过该类实例进行。

**将Json转为对象**

~~~java
String json = "{\"name\":\"张三\",\"age\":18}";
Gson gson = new Gson();
//转换为集合
Map<String,Object> stuMap = gson.fromJson(json, Map.class);
//如果编写了相应的类也可以转换为指定对象
Student fromJson = gson.fromJson(json, Student.class);
~~~

**将对象转为json**

~~~java
Student stu = new Student("李四", 23);
Gson gson = new Gson();
String json = gson.toJson(stu);//{"name":"李四","age":23}
		
Map<String , Object> map = new HashMap<String, Object>();
map.put("name", "孙悟空");
map.put("age", 30);
String json2 = gson.toJson(map);//{"age":30,"name":"孙悟空"}
		
List<Student> list = new ArrayList<Student>();
list.add(new Student("八戒", 18));
list.add(new Student("沙僧", 28));
list.add(new Student("唐僧", 38));
//[{"name":"八戒","age":18},{"name":"沙僧","age":28},{"name":"唐僧","age":38}]
String json3 = gson.toJson(list);	

~~~



​	



## Echart表格的结合

### 入门例子1：

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Echart</title>
</head>
<body>
    <div>
      <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
    </div>
    
<script src="https://cdn.bootcss.com/echarts/4.2.1-rc1/echarts-en.min.js"></script>
<script type="text/javascript">
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'));

    // 指定图表的配置项和数据
    var option = {
        title: { // 图表的标题
            text: 'ECharts 入门示例'
        },
        tooltip: {},
        legend: { // lengend 图表进行说明
            data:['销量','飞机']
        },
        xAxis: {  // 图表的x轴
            data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
        },
        yAxis: {},
        series: [{
            name: '销量', // 这里的值需要是lengend中的data数组中的值
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
        }]
    };

    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
    </script>
</body>
</html>
~~~

![1563266039190](img/jq/2.png)

### 入门例子2：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Echart</title>
</head>
<body>
    <div>
      <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
    </div>
    
<script src="https://cdn.bootcss.com/echarts/4.2.1-rc1/echarts-en.min.js"></script>
<script type="text/javascript">
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'));

    myChart.setOption({
    series : [
        {
            name: '访问来源',
            type: 'pie',
            radius: '55%',
            data:[
                {value:235, name:'视频广告'},
                {value:274, name:'联盟广告'},
                {value:310, name:'邮件营销'},
                {value:335, name:'直接访问'},
                {value:400, name:'搜索引擎'}
            ]
        }
    ]
});
    </script>
</body>
</html>
~~~

![1563266361359](img/jq/3.png)

### 入门例子4--条形图堆叠

~~~HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Echart</title>
</head>
<body>
<div>
    <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
</div>

<script src="https://cdn.bootcss.com/echarts/4.2.1-rc1/echarts-en.min.js"></script>
<script type="text/javascript">
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'));

    // 指定图表的配置项和数据
    var options = {
        title: {
            text: '折线图堆叠'
        },
        tooltip: {
            trigger: 'axis'
        },
        legend: {
            data:['邮件营销','联盟广告','视频广告','直接访问','搜索引擎']
        },
        grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
        },
        xAxis: {
            type: 'category',
            boundaryGap: false,
            data: ['周一','周二','周三','周四','周五','周六','周日']
        },
        yAxis: {
            type: 'value'
        },
        series: [
            {
                name:'邮件营销',
                type:'line',
                stack: '总量',
                data:[120, 132, 101, 134, 90, 230, 210]
            },
            {
                name:'联盟广告',
                type:'line',
                stack: '总量',
                data:[220, 182, 191, 234, 290, 330, 310]
            },
            {
                name:'视频广告',
                type:'line',
                stack: '总量',
                data:[150, 232, 201, 154, 190, 330, 410]
            },
            {
                name:'直接访问',
                type:'line',
                stack: '总量',
                data:[320, 332, 301, 334, 390, 330, 320]
            },
            {
                name:'搜索引擎',
                type:'line',
                stack: '总量',
                data:[820, 932, 901, 934, 1290, 1330, 1320]
            }
        ]
    };
    myChart.setOption(options);
</script>
</body>
</html>
~~~

如图：

![1563883653924](img/jq/4.png)

#### 桑甚图

node，link

地址：https://echarts.apache.org/zh/option.html#series-sankey>

