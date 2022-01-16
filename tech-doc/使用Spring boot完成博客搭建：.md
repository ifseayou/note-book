# 使用Spring boot完成博客搭建

boot用来开发单一服务的框架，是cloud的基础，依赖于Spring框架。

**环境**

jdk1.8，gradle3.5，

## 初识gradle

**gradle的安装和配置：[Gradle官网](https://gradle.org/install/)**

**如何卸载gradle**

> That depends on how you installed it. If you downloaded the zipped Gradle distribution, delete the directory that you unzipped it to. If you installed Gradle with a package manager, use the same package manager to uninstall it. In any case, delete the ‘<user_home>/.gradle’ directory. If you manually added environment variables like ‘GRADLE_HOME’, remove them as well.

从一个例子中理解gradle编译的过程，有如下的几个步骤：

* 在浏览器中输入 `https://start.spring.io/` ，填写好相关的包，并选择web
* 并生成一个项目（`spingboot-gradle`是我的项目名）
* 然后下载下来，并且解压。可以看到该目录下的内容：

~~~shell
isea_you@isea MINGW64 /g/boot_cloud/self-learn/spingboot-gradle (master)
$ ll
total 15
-rw-r--r-- 1 isea_you 197121  393 7月  19 17:33 build.gradle
drwxr-xr-x 1 isea_you 197121    0 7月  20 01:34 gradle/
-rwxr-xr-x 1 isea_you 197121 5305 7月  19 17:33 gradlew*
-rw-r--r-- 1 isea_you 197121 2269 7月  19 17:33 gradlew.bat
-rw-r--r-- 1 isea_you 197121  674 7月  19 17:33 HELP.md
-rw-r--r-- 1 isea_you 197121  101 7月  19 17:33 settings.gradle
drwxr-xr-x 1 isea_you 197121    0 7月  20 01:34 src/

# 执行构建命令，将该项目构建
gradle build # 第一次构建需要等一会，构建完成以后该层目录会多一层 bulid/libs文件夹，该文件夹下有可以执行的jar包，也就是项目产生的jar包。

# 然后我们就可以运行构建好的jar包
java -jar ****.jar
~~~

**build.gradle** 文件：该文件是整个项目的构建脚本 

**gradle/wraper** 在用户没有下载安装 gradle的情况下，自动为用户构建，好处是可以统一构建工具及其版本。

 ## hello-world项目

在上面的项目的同级目录下面建立一个**hello-world** 文件夹，并将上面项目中的除了***.gradle，bulid***的文件全部拷贝到当前目录，然后在当前的**hello-world** 项目下执行 `gradle bulid` 命令，构建成功之后，执行可以运行的jar包，然后在浏览器访问 `localhost:8080`，

### 修改中央仓库

和**Maven** 的原理一样，我们可以修改中央仓库为国内的中央仓库，以突破网络限制，主要修改 ***build.gradle*** 文件。如下：

~~~json
plugins {
	id 'org.springframework.boot' version '2.1.6.RELEASE'
	id 'java'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.isea'
version = '1.0.0'
sourceCompatibility = '1.8'

repositories {
	// mavenCentral()
	maven{ // 这里是需要修改的地方
		url:"http://maven.aliyun.com/nexus/content/groups/public/"
	}
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

~~~

**Application主类**

~~~java
// @SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan


/**
	在Configuration注解和Bean注解结合使用，就可以组成spring的配置类，就可以代替原来的springxml配置文件，configuration的注解标识这个类可以使用Spring IOC容器最为bean对应的来源，然后bean注解就会告诉spring一个带有bean注解的方法将会返回一个对象，而这个对象会被注册为spring应用上下文中的一个bean。
	
	EnableAutoConfiguration 能够实现自动配置spring的上下文，	
*/
~~~

**编写Controller**

~~~java
@RestController // =@Controller + @ResponseBody(返回给浏览器)
public class HelloController {
    @RequestMapping("/hello")
    public String hello(){
        return "Hello world...";
    }
}
~~~

启动Application，然后在浏览器输入`localhost:8080/hello` 可以得到相应： `Hello world...`

**Wrapper** 包装纸的意思

`gradle/wrapper/gradle-properties` 文件中的配置是 `gradlew 和gradlew.bat`两个构建脚本的配置，这两个构建脚本是按照配置来执行构建的。接下来我们对该进行进行构建，执行`gradlew build` ，也可以使用`gradle build`

**运行项目的方式：**

* `java -jar`
* `gradle bootRun`  spring  boot gradle plugin 插件
* 在项目中按照`Java Application`的方式去运行

## Thymeleaf

Thymeleaf是一个模板引擎。能够处理`html,xml,JavaScript,CSS甚至存文本`

**一些表达式**

~~~JavaScript 
// 变量表达式
${...}
  
// 消息表示式，也叫做文本外部化，国际化
#{...} 

// 选择表达式，也叫做星号表达式，与变量表达式的区别是：
// 选择表达式取值于当前选择的对象映射，而不是整个上下文的变量映射
*{...}
  
// 链接表达式
@{...}

// 分段表达式
th:insert 或者是 th:replace

// 字面量
文本，数字，bool，null，等等

// 无操作
_

// 迭代器
th:each

// 迭代器的状态变量，
index 索引从0开始
count 从1开始
size  个数
current 当前的迭代的变量
even/odd  奇偶
first 当前的迭代是不是第一个 
last 当前的迭代是不是最后一个

// 条件语句
th:if , th:unless   th:switch 后天接th:case 如果中了当前的case ，后面的是不会在判断了的 


// 模板布局
在真实的网站中会有一些共用的部分，比如网站的名称，标题，页脚，菜单项。在这种情况下，我们可以将其提取出来作为一个模板片段拿来供其他的页面来引用，如何定义模板呢?
    使用 th:fragment 来定义 , 或者是使用div 的id选择器来定义 ，然后引用之
    使用 th:insert 来引用
    
模板有三种引入方式：
    th:insert 简单插入指定的片段作为正文的标签
    th:replace 用引用的片段来替换主标签
    th:include 不推荐使用了

// 属性优先级
有一个表，查询即可，如果一个标签定义了两个属性，不管谁在前，谁在后，只要优先级不一样，是不影响执行顺序的    
// 注释
    HTML的注释<!--这里是注释-->
    thymeleaf解释器级别的注释块 删除<!--/* 和 */ --> 之间的内容
    <!--/*-->
    	<div> you are so beautiful..</div>
    <!--*/-->
    中间包含的内容在静态的时候会被显示，当这个模板在被执行的时候thymeleaf就会解析这块注释的内容，此时中间的内容就会被注释掉。
    
    原型注释块：模板静态打开时，会被注释掉，执行模板时，这些注释的代码就会被显示出来
    <!--/*/
    	<div>you are so beautiful</div>
    /*/-->
 
// 内联表达式
	我们使用thymeleaf来实现很多功能，比如说来置换我们的HTML文本，但是有些时候将表达式写到文本里面，这样的方式我们称之为内联，我们来看看内联表达式：
    [[]] 对应于th:text 会对特殊的符号进行一个转义
    [()] 对应于th:utext 
    <p> the message is "[(${msg})]" </p> msg的内容该是什么样就是什么样
    
   禁止用内联表达式：在标签添加：<p th:inline="none"> </p>
   
   CSS和JavaScript都可以设置内联
   
// 表达式基本对象
   在thymeleaf中，模板初始化的时候，有一些对象和变量映射始终是可以被访问的， 也即这些变量和对象在thymeleaf初始化的时候，已经初始化好了，已经存在于上下文的映射里面了，这些对象是可以随时被访问的。这些对象我们称之为表达式的基本对象。这些对象主要有哪些呢？
   
   #ctx:上下文对象，是org.thymeleaf.context.IContext（Java程序里面的）或者org.thymeleaf.context.IWebContext（Web程序里面的） 的实现。
   
// 表达式工具对象
   thymeleaf提供了很多的表达式工具对象，通过这些工具对象来提供一些非常有用的方法
   
   执行信息：#execInfo 表达式对象提供的有关在thymeleaf标准表达式内正在处理的模板的有用信息
   消息信息：#message 在表达式中获取外部化消息的实用方式
~~~

### Spring boot 和 thymeleaf的整合

