# JUnit 实战

代码部分：参考[GitHub地址](<https://github.com/ifseayou/virgin/tree/master/virgin-juint>)

## JUnit核心

### JUnit 核心对象

| JUnit核心概念       | 责任                                                         |
| ------------------- | ------------------------------------------------------------ |
| Assert              | 让你定义你想测试的条件，当条件成立的时候assert方法保持沉默，<br>但是条件不成立的时候，抛出异常 |
| 测试                | 一个以@Test注释的方法定义一个测试，为了运行这个方法，JUint会创建<br>一个包含类的新实例，然后在调用这个被注释的方法， |
| **测试类/测试用例** | 一个测试类是@Test方法的容器                                  |
| Suite/测试集        | Suite允许你将测试类归为一成一组                              |
| Runner              | Runner类用来测试，JUit4向后兼容，可以运行JUnit3的测试        |

### 测试运行器

下表列出了运行器

| 运行器                        | 目的                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| org.junit.runner.JUnit3.8     | 将测试用例作为JUnit3.8的测试用例来启动                       |
| org.junit.runner.JUnit4       | 将测试用例作为JUnit4的测试用例来启动                         |
| org.juit.runner.Parameterized | 该测试运行器可以使用不同的参数来运行相同的测试集             |
| org.junit.runner.Suit         | Suite是一个包含不同的测试的容器，同时‘Suite也是一个运行器<br>可以运行所有加上了@Test的方法 |

注：**可以使用`@RunWith`注解来指定特定的运行器，如果没有JUnit会使用默认的运行器**。

~~~
facade # 正面，前面，facade pattern 一种对多个接口再次进行统一和规范的设计模式
~~~

设计Suite $ a ,suite,of $ 是为了运行一个或者是多个测试用例，如果你没有提供一个Suite，那么测试运行器会自己创建一个Suite。

### 对于Controller的测试

控制器：$同客户端交互，控制并管理每个请求的处理$ 