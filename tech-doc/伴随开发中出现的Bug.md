#  伴随开发中出现的Bug

```java
Spring Hibernate - Could not obtain transaction-synchronized Session for current thread

// 解决：在Dao层的方法上添加 @Transactional  Did you try adding an @Transactional to your DAO create method
```

<hr>

~~~javascript
// 在JavaScript中如何将一个可以转化为数字的类型转为Number类型
parseInt(**)
~~~

<hr>

~~~java
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'arithmeticCalculatorImpl' defined in*.class]: BeanPostProcessor before instantiation of bean failed; nested exception is java.lang.NoClassDefFoundError: org/aspectj/weaver/reflect/ReflectionWorld$ReflectionWorldException

// NoClassDefFoundError:是重点，这基本都是由于没有定义该类，即没有导入依赖，解决的方法：
~~~

~~~xml
<dependency>
    <groupId>aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.5.3</version>
</dependency>
~~~

<hr>
~~~java
required string parameter 'XXX'is not present 
// 该问题基本上是以为Spring 的参数名没有对应上，所以最好的方式就是名字叫一样的；我这里是由于打包的问题。原来是需要打包parent，也即root，root下面的包才会获的更新。
~~~

<hr>




