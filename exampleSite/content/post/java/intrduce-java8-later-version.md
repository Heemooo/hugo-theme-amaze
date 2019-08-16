+++
title = "SpringBoot自定义配置文件"
description = ""
tags = [
    "Java8",
	"Java9",
	"Java10",
    "Java11"
]
date = "2019-07-03"
categories = [
	"Java"
]
+++
# Java8、9、10、11简介

> 不知不觉Java已经更新到了Java12，但是大多数从事Java相关开发人员依然在使用Java8，对于Jdk8中的新特性也不够了解，甚至仍在使用Java7以前的语法和方式编写Java程序，因为Java11是2018发布的长期支持版本（到2026年9月），而Java11以后的12到下一个长期支持版本大约要在2021年发布，作为开发人员，部分企业先后已升级到Jdk11，因此我们有必要了解Java8-11之间究竟更新了什么，增加了什么新特性。

## 一、Java8

毫无疑问，[Java 8发行版](http://www.oracle.com/technetwork/java/javase/8train-relnotes-latest-2153846.html)是自Java 5（发行于2004，已经过了相当一段时间了）以来最具革命性的版本。Java 8 为Java语言、编译器、类库、开发工具与JVM（Java虚拟机）带来了大量新特性。在这篇教程中，我们将一一探索这些变化，并用真实的例子说明它们适用的场景。

### 1.Java语言的新特性

#### 1.1Lambda表达式与Functional接口

Lambda表达式（也称为闭包）是整个Java 8发行版中最受期待的在Java语言层面上的改变，Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中），或者把代码看成数据：函数式程序员对这一概念非常熟悉。在JVM平台上的很多语言（Groovy，[Scala](http://www.javacodegeeks.com/tutorials/scala-tutorials/)，……）从一开始就有Lambda，但是Java程序员不得不使用毫无新意的匿名类来代替lambda。

```java
Arrays.asList( "a", "b", "d" ).forEach( e -> System.out.println( e ) );
```

Lambda可以引用类的成员变量与局部变量（如果这些变量不是final的话，它们会被隐含的转为final，这样效率更高）。例如，下面两个代码片段是等价的：

```java
String separator = ",";
Arrays.asList( "a", "b", "d" ).forEach( 
    ( String e ) -> System.out.print( e + separator ) );
```

```java
final String separator = ",";
Arrays.asList( "a", "b", "d" ).forEach( 
    ( String e ) -> System.out.print( e + separator ) );
```

Lambda可能会返回一个值。返回值的类型也是由编译器推测出来的。如果lambda的函数体只有一行的话，那么没有必要显式使用return语句。下面两个代码片段是等价的：

```java
Arrays.asList( "a", "b", "d" ).sort( ( e1, e2 ) -> e1.compareTo( e2 ) );
```

```java
Arrays.asList( "a", "b", "d" ).sort( ( e1, e2 ) -> {
    int result = e1.compareTo( e2 );
    return result;
} );
```
语言设计者投入了大量精力来思考如何使现有的函数友好地支持lambda。最终采取的方法是：增加函数式接口的概念。函数式接口就是一个具有一个方法的普通接口。像这样的接口，可以被隐式转换为lambda表达式。java.lang.Runnable与java.util.concurrent.Callable是函数式接口最典型的两个例子。在实际使用过程中，函数式接口是容易出错的：如有某个人在接口定义中增加了另一个方法，这时，这个接口就不再是函数式的了，并且编译过程也会失败。为了克服函数式接口的这种脆弱性并且能够明确声明接口作为函数式接口的意图，Java 8增加了一种特殊的注解@FunctionalInterface（Java 8中所有类库的已有接口都添加了@FunctionalInterface注解）。让我们看一下这种函数式接口的定义：
```java
@FunctionalInterface
public interface Functional {
    void method();
}
```
需要记住的一件事是：默认方法与静态方法并不影响函数式接口的契约，可以任意使用：
```java
@FunctionalInterface
public interface FunctionalDefaultMethods {
    void method();
         
    default void defaultMethod() {            
    }        
}
```
#### 1.2 接口的默认方法与静态方法

Java 8用默认方法与静态方法这两个新概念来扩展接口的声明。[默认方法](http://www.javacodegeeks.com/2014/04/java-8-default-methods-what-can-and-can-not-do.html)使接口有点像Traits（Scala中特征(trait)类似于Java中的Interface，但它可以包含实现代码，也就是目前Java8新增的功能），但与传统的接口又有些不一样，它允许在已有的接口中添加新方法，而同时又保持了与旧版本代码的兼容性。

默认方法与抽象方法不同之处在于抽象方法必须要求实现，但是默认方法则没有这个要求。相反，每个接口都必须提供一个所谓的默认实现，这样所有的接口实现者将会默认继承它（如果有必要的话，可以覆盖这个默认实现）。让我们看看下面的例子：

```java
interface Defaulable {
    // Interfaces now allow default methods, the implementer may or 
    // may not implement (override) them.
    default String notRequired() { 
        return "Default implementation"; 
    }        
}
         
class DefaultableImpl implements Defaulable {
}
     
class OverridableImpl implements Defaulable {
    @Override
    public String notRequired() {
        return "Overridden implementation";
    }
}
```

Defaulable接口用关键字default声明了一个默认方法notRequired()，Defaulable接口的实现者之一DefaultableImpl实现了这个接口，并且让默认方法保持原样。Defaulable接口的另一个实现者OverridableImpl用自己的方法覆盖了默认方法。

Java 8带来的另一个有趣的特性是接口可以声明（并且可以提供实现）静态方法。例如：

```java
interface DefaulableFactory {
    //Supplier 是一个创建对象实例的接口
    static Defaulable create( Supplier< Defaulable > supplier ) {
        //调用get方法，此时会调用对象的构造方法，获得真正的对象
        return supplier.get();
    }
}
```

下面的一小段代码片段把上面的默认方法与静态方法黏合到一起。

```java
public static void main( String[] args ) {
    //DefaultableImpl::new 会返回一个Supplier对象
    Defaulable defaulable = DefaulableFactory.create( DefaultableImpl::new );
    System.out.println( defaulable.notRequired() );
    
    defaulable = DefaulableFactory.create( OverridableImpl::new );
    System.out.println( defaulable.notRequired() );
}
```

这个程序的控制台输出如下：

```java
Default implementation
Overridden implementation
```

在JVM中，默认方法的实现是非常高效的，并且通过字节码指令为方法调用提供了支持。默认方法允许继续使用现有的Java接口，而同时能够保障正常的编译过程。这方面好的例子是大量的方法被添加到java.util.Collection接口中去：stream()，parallelStream()，forEach()，removeIf()，……

尽管默认方法非常强大，但是在使用默认方法时我们需要小心注意一个地方：在声明一个默认方法前，请仔细思考是不是真的有必要使用默认方法，因为默认方法会带给程序歧义，并且在复杂的继承体系中容易产生编译错误。

#### 1.3 方法引用

方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。

下面，我们以定义了4个方法的Car这个类作为例子，区分Java中支持的4种不同的方法引用。

```java
public static class Car {
    public static Car create( final Supplier< Car > supplier ) {
        return supplier.get();
    }              
         
    public static void collide( final Car car ) {
        System.out.println( "Collided " + car.toString() );
    }
         
    public void follow( final Car another ) {
        System.out.println( "Following the " + another.toString() );
    }
         
    public void repair() {   
        System.out.println( "Repaired " + this.toString() );
    }
}
```

第一种方法引用是构造器引用，它的语法是Class::new，或者更一般的Class< T >::new。请注意构造器没有参数。

```java
final Car car = Car.create( Car::new );
final List< Car > cars = Arrays.asList( car );
```

第二种方法引用是静态方法引用，它的语法是Class::static_method。请注意这个方法接受一个Car类型的参数。

```java
cars.forEach( Car::collide );
```

第三种方法引用是特定类的任意对象的方法引用，它的语法是Class::method。请注意，这个方法没有参数。

```java
cars.forEach( Car::repair );
```

最后，第四种方法引用是特定对象的方法引用，它的语法是instance::method。请注意，这个方法接受一个Car类型的参数

```java
final Car police = Car.create( Car::new);
cars.forEach( police::follow );
```

#### 1.4 重复注解

自从Java 5引入了[注解](http://www.javacodegeeks.com/2012/08/java-annotations-explored-explained.html)机制，这一特性就变得非常流行并且广为使用。然而，使用注解的一个限制是相同的注解在同一位置只能声明一次，不能声明多次。Java 8打破了这条规则，引入了重复注解机制，这样相同的注解可以在同一地方声明多次。

重复注解机制本身必须用@Repeatable注解。事实上，这并不是语言层面上的改变，更多的是编译器的技巧，底层的原理保持不变。让我们看一个快速入门的例子：

```java
@Target(ElementType.TYPE)  
@Retention(RetentionPolicy.RUNTIME)
public @interface Persons {
	Person[] value();
}
@Repeatable(Persons.class)
public @interface Person{
	String role() default "";
}
@Person(role="CEO")
@Person(role="husband")
@Person(role="father")
@Person(role="son")
public class Man {
	String name="";
}
```

[Java注解——Repeatable](https://blog.csdn.net/ljcgit/article/details/81708679)

#### 1.5 更好的类型推测机制

Java 8在类型推测方面有了很大的提高。在很多情况下，编译器可以推测出确定的参数类型，这样就能使代码更整洁。让我们看一个例子：

```java
public class Value< T > {
    public static< T > T defaultValue() { 
        return null; 
    }
     
    public T getOrDefault( T value, T defaultValue ) {
        return ( value != null ) ? value : defaultValue;
    }
}
public class TypeInference {
    public static void main(String[] args) {
        final Value<String> value = new Value<>();
        value.getOrDefault( "22", Value.defaultValue() );
    }
}
//JDK8以前
public class TypeInference {
    public static void main(String[] args) {
        final Value<String> value = new Value<String>();
        value.getOrDefault( "22", Value.<String>defaultValue() );
    }
}
```

#### 1.6 扩展注解的支持

Java 8扩展了注解的上下文。现在几乎可以为任何东西添加注解：局部变量、泛型类、父类与接口的实现，就连方法的异常也能添加注解。

```java
ElementType.TYPE_USE//表示注解可以再任何用到类型的地方使用，比如创建对象，类型转换，实现接口，抛出异常
ElementType.TYPE_PARAMETER//表示该注解能写在类型参数的声明语句中
```
示例如下：

```java
public class Annotations {
    @Retention( RetentionPolicy.RUNTIME )
    @Target( { ElementType.TYPE_USE, ElementType.TYPE_PARAMETER } )
    public @interface NonEmpty {        
    }
         
    public static class Holder< @NonEmpty T > extends @NonEmpty Object {
        public void method() throws @NonEmpty Exception {           
        }
    }
         
    @SuppressWarnings( "unused" )
    public static void main(String[] args) {
        final Holder< String > holder = new @NonEmpty Holder< String >();       
        @NonEmpty Collection< @NonEmpty String > strings = new ArrayList<>();       
    }
}
```

### 二、Java编译器的新特性

#### 3.1 参数名字

很长一段时间里，Java程序员一直在发明不同的方式使得[方法参数的名字能保留在Java字节码](http://www.javacodegeeks.com/2014/04/constructormethod-parameters-metadata-available-via-reflection-in-jdk-8.html)中，并且能够在运行时获取它们（比如，[Paranamer类库](https://github.com/paul-hammant/paranamer)）。最终，在Java 8中把这个强烈要求的功能添加到语言层面（通过反射API与Parameter.getName()方法）与字节码文件（通过新版的javac的–parameters选项）中。

```java
  public static void main(String[] args) throws Exception {
        Method method = ParameterNames.class.getMethod( "main", String[].class );
        for( final Parameter parameter: method.getParameters() ) {
            System.out.println( "Parameter: " + parameter.getName() );
        }
    }
```

如果不使用–parameters参数来编译这个类，然后运行这个类，会得到下面的输出：

```java
`Parameter: arg0`
```

如果使用–parameters参数来编译这个类，程序的结构会有所不同（参数的真实名字将会显示出来）：

```java
`Parameter: args`
```

### 三、 Java 类库的新特性

Java 8 通过增加大量新类，扩展已有类的功能的方式来改善对并发编程、函数式编程、日期/时间相关操作以及其他更多方面的支持。

#### 4.1 Optional

到目前为止，[臭名昭著的空指针异常](http://examples.javacodegeeks.com/java-basics/exceptions/java-lang-nullpointerexception-how-to-handle-null-pointer-exception/)是导致Java应用程序失败的最常见原因。以前，为了解决空指针异常，[Google公司著名的Guava项目](http://code.google.com/p/guava-libraries/)引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。受到[Google Guava](http://code.google.com/p/guava-libraries/)的启发，Optional类已经成为Java 8类库的一部分。

Optional实际上是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。更多详情请参考[官方文档](http://docs.oracle.com/javase/8/docs/api/)。

在 Java 8 之前，任何访问对象方法或属性的调用都可能导致 *NullPointerException*：

```java
String isocode = user.getAddress().getCountry().getIsocode().toUpperCase();
```

在这个小示例中，如果我们需要确保不触发异常，就得在访问每一个值之前对其进行明确地检查：

```java
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        Country country = address.getCountry();
        if (country != null) {
            String isocode = country.getIsocode();
            if (isocode != null) {
                isocode = isocode.toUpperCase();
            }
        }
    }
}
```

为了简化这个过程，我们来看看用 *Optional*  类是怎么做的:

```java
public class User {
    private Address address;

    public Optional<Address> getAddress() {
        return Optional.ofNullable(address);
    }

    // ...
}
public class Address {
    private Country country;

    public Optional<Country> getCountry() {
        return Optional.ofNullable(country);
    }

    // ...
}
```

```java
String result = Optional.ofNullable(user)
  .flatMap(User::getAddress)
  .flatMap(Address::getCountry)
  .map(Country::getIsocode)
  .orElse("default");
```



更多详情请参考[官方文档](http://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)

 [理解、学习与使用 JAVA 中的 OPTIONAL](https://www.cnblogs.com/zhangboyu/p/7580262.html)

#### 4.2 Stream

最新添加的[Stream API](http://www.javacodegeeks.com/2014/05/the-effects-of-programming-with-java-8-streams-on-algorithm-performance.html)（java.util.stream） 把真正的函数式编程风格引入到Java中。这是目前为止对Java类库最好的补充，因为Stream API可以极大提供Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

Stream API极大简化了集合框架的处理（但它的处理的范围不仅仅限于集合框架的处理，这点后面我们会看到）。让我们以一个简单的Task类为例进行介绍：

```java
public class Streams  {
    private enum Status {
        OPEN, CLOSED
    };
     
    private static final class Task {
        private final Status status;
        private final Integer points;
 
        Task( final Status status, final Integer points ) {
            this.status = status;
            this.points = points;
        }
         
        public Integer getPoints() {
            return points;
        }
         
        public Status getStatus() {
            return status;
        }
         
        @Override
        public String toString() {
            return String.format( "[%s, %d]", status, points );
        }
    }
}
```
Task类有一个分数的概念（或者说是伪复杂度），其次是还有一个值可以为OPEN或CLOSED的状态.让我们引入一个Task的小集合作为演示例子：

```java
final Collection< Task > tasks = Arrays.asList(
    new Task( Status.OPEN, 5 ),
    new Task( Status.OPEN, 13 ),
    new Task( Status.CLOSED, 8 ) 
);
```

我们下面要讨论的第一个问题是所有状态为OPEN的任务一共有多少分数？在Java 8以前，一般的解决方式用foreach循环，但是在Java 8里面我们可以使用stream：一串支持连续、并行聚集操作的元素。

```java
// Calculate total points of all active tasks using sum()
final long totalPointsOfOpenTasks = tasks
    .stream()
    .filter( task -> task.getStatus() == Status.OPEN )
    .mapToInt( Task::getPoints )
    .sum();
         
System.out.println( "Total points: " + totalPointsOfOpenTasks );
```

程序在控制台上的输出如下：

```
Total points: 18
```

这里有几个注意事项。第一，task集合被转换化为其相应的stream表示。然后，filter操作过滤掉状态为CLOSED的task。下一步，mapToInt操作通过Task::getPoints这种方式调用每个task实例的getPoints方法把Task的stream转化为Integer的stream。最后，用sum函数把所有的分数加起来，得到最终的结果。

Stream API、[Lambda表达式](http://www.importnew.com/11908.html#lambdaAndFunctional)与[方法引用](http://www.importnew.com/11908.html#methodReferences)在[接口默认方法与静态方法](http://www.importnew.com/11908.html#defaultAndStaticMethod)的配合下是Java 8对现代软件开发范式的回应。更多详情请参考[官方文档](http://docs.oracle.com/javase/tutorial/collections/streams/index.html)。



#### 4.3 Date/Time API (JSR 310)

Java 8通过发布[新的Date-Time API (JSR 310)](https://jcp.org/en/jsr/detail?id=310)来进一步加强对日期与时间的处理。对日期与时间的操作一直是Java程序员最痛苦的地方之一。标准的 java.util.Date以及后来的java.util.Calendar一点没有改善这种情况（可以这么说，它们一定程度上更加复杂）。

这种情况直接导致了[Joda-Time](http://www.joda.org/joda-time/)——一个可替换标准日期/时间处理且功能非常强大的Java API的诞生。Java 8[新的Date-Time API (JSR 310)](https://jcp.org/en/jsr/detail?id=310)在很大程度上受到[Joda-Time](http://www.joda.org/joda-time/)的影响，并且吸取了其精髓。新的java.time包涵盖了[所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作](http://www.javacodegeeks.com/2014/03/whats-new-in-java-8-date-api.html)。在设计新版API时，十分注重与旧版API的兼容性：不允许有任何的改变（从java.util.Calendar中得到的深刻教训）。如果需要修改，会返回这个类的一个新实例。

让我们用例子来看一下新版API主要类的使用方法。第一个是Clock类，它通过指定一个时区，然后就可以获取到当前的时刻，日期与时间。Clock可以替换System.currentTimeMillis()与TimeZone.getDefault()。

```java
// Get the system clock as UTC offset 
final Clock clock = Clock.systemUTC();
System.out.println(clock.instant());
System.out.println(clock.millis());
```

下面是程序在控制台上的输出：

```
2019-06-16T11:21:28.160485900Z
1560684088320
```

更多详情请参考[官方文档](http://docs.oracle.com/javase/tutorial/datetime/index.html)。



#### 4.4 JavaScript引擎Nashorn

Nashorn，一个新的JavaScript引擎随着Java 8一起公诸于世，它允许在JVM上开发运行某些JavaScript应用。Nashorn就是javax.script.ScriptEngine的另一种实现，并且它们俩遵循相同的规则，允许Java与JavaScript相互调用。下面看一个例子：

```java
ScriptEngineManager manager = new ScriptEngineManager();
ScriptEngine engine = manager.getEngineByName( "JavaScript" );
         
System.out.println( engine.getClass().getName() );
System.out.println( "Result:" + engine.eval( "function f() { return 1; }; f() + 1;" ) );
```

下面是程序在控制台上的输出：

```
jdk.nashorn.api.scripting.NashornScriptEngine
Result: 2
```

我们在后面的[Java新工具](http://www.importnew.com/11908.html#newJavaTool)章节会再次谈到Nashorn。



#### 4.5 Base64

在Java 8中，[Base64编码](http://www.javacodegeeks.com/2014/04/base64-in-java-8-its-not-too-late-to-join-in-the-fun.html)已经成为Java类库的标准。它的使用十分简单，下面让我们看一个例子：

```java
import java.nio.charset.StandardCharsets;
import java.util.Base64;
 
public class Base64s {
    public static void main(String[] args) {
        final String text = "Base64 finally in Java 8!";
         
        final String encoded = Base64
            .getEncoder()
            .encodeToString( text.getBytes( StandardCharsets.UTF_8 ) );
        System.out.println( encoded );
         
        final String decoded = new String( 
            Base64.getDecoder().decode( encoded ),
            StandardCharsets.UTF_8 );
        System.out.println( decoded );
    }
}
```

程序在控制台上输出了编码后的字符与解码后的字符：

```
QmFzZTY0IGZpbmFsbHkgaW4gSmF2YSA4IQ==
Base64 finally in Java 8!
```

Base64类同时还提供了对URL、MIME友好的编码器与解码器（Base64.getUrlEncoder() / Base64.getUrlDecoder(), Base64.getMimeEncoder() / Base64.getMimeDecoder()）。



#### 4.6 并行（parallel）数组

Java 8增加了大量的新方法来对数组进行并行处理。

#### 4.7 并发（Concurrency）

在新增Stream机制与lambda的基础之上，在java.util.concurrent.ConcurrentHashMap中加入了一些新方法来支持聚集操作（对性能有所提升）。同时也在java.util.concurrent.ForkJoinPool类中加入了一些新方法来支持共有资源池（common pool）。

新增的java.util.concurrent.locks.StampedLock类提供一直基于容量的锁，这种锁有三个模型来控制读写操作（它可以说是java.util.concurrent.locks.ReadWriteLock类的替代者）。

[ConcurrentHashMap在Java1.8中的改进](https://www.cnblogs.com/everSeeker/p/5601861.html)

## 二、Java9

#### 1.模块化

Java 9 的定义功能是一套全新的模块系统。当代码库越来越大，创建复杂，盘根错节的“意大利面条式代码”的几率呈指数级的增长。这时候就得面对两个基础的问题: 很难真正地对代码进行封装, 而系统并没有对不同部分（也就是 JAR 文件）之间的依赖关系有个明确的概念。每一个公共类都可以被类路径之下任何其它的公共类所访问到, 这样就会导致无意中使用了并不想被公开访问的 API。此外，类路径本身也存在问题: 你怎么知晓所有需要的 JAR 都已经有了, 或者是不是会有重复的项呢? 模块系统把这俩个问题都给解决了。

模块化的 JAR 文件都包含一个额外的模块描述器。在这个模块描述器中, 对其它模块的依赖是通过 “requires” 来表示的。另外, “exports” 语句控制着哪些包是可以被其它模块访问到的。所有不被导出的包默认都封装在模块的里面。如下是一个模块描述器的示例，存在于 “module-info.java” 文件中:

```java
module blog {
  exports com.pluralsight.blog;
 
  requires cms;
}
```

#### 1.2 Linking

当你使用具有显式依赖关系的模块和模块化的 JDK 时，新的可能性出现了。你的应用程序模块现在将声明其对其他应用程序模块的依赖以及对其所使用的 JDK 模块的依赖。为什么不使用这些信息创建一个最小的运行时环境，其中只包含运行应用程序所需的那些模块呢？ 这可以通过 Java 9 中的新的 jlink 工具实现。你可以创建针对应用程序进行优化的最小运行时映像而不需要使用完全加载 JDK 安装版本。

#### 1.3 JShell

#### 1.4 改进的 Javadoc

#### 1.5 集合工厂方法

通过下面 的方式直接声明不可变集合

```java
Set<Integer> ints = Set.of(1, 2, 3);
List<String> strings = List.of("first", "second");
```

#### 1.6 改进的 Stream API

#### 1.7 私有接口方法

Java 8 为我们带来了接口的默认方法。 接口现在也可以包含行为，而不仅仅是方法签名。 但是，如果在接口上有几个默认方法，代码几乎相同，会发生什么情况？ 通常，您将[重构](http://www.amazon.cn/gp/product/B003BY6PLK/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&tag=importnew-23&linkCode=as2&camp=536&creative=3200&creativeASIN=B003BY6PLK)这些方法，调用一个可复用的私有方法。 但默认方法不能是私有的。 将复用代码创建为一个默认方法不是一个解决方案，因为该辅助方法会成为公共API的一部分。 使用 Java 9，您可以向接口添加私有辅助方法来解决此问题：

```java
public interface MyInterface {
 
    void normalInterfaceMethod();
 
    default void interfaceMethodWithDefault() {  init(); }
 
    default void anotherDefaultMethod() { init(); }
 
    // This method is not part of the public API exposed by MyInterface
    private void init() { System.out.println("Initializing"); }
```

#### 1.8 HTTP

Java 9 中有新的方式来处理 HTTP 调用。这个迟到的特性用于代替老旧的 `HttpURLConnection` API，并提供对 WebSocket 和 HTTP/2 的支持。注意：新的 HttpClient API 在 Java 9 中以所谓的孵化器模块交付。也就是说，这套 API 不能保证 100% 完成。然而http已经更新到http/3了

#### 1.9 多版本兼容 JAR

## 三、Java10

#### 1.1 局部变量类型推断

```java
final var clock = Clock.systemUTC();
System.out.println(clock.instant());
System.out.println(clock.millis());
```

这个新功能将为Java增加一些语法糖 - 简化它并改善开发者体验。新的语法将减少与编写Java相关的冗长度，同时保持对静态类型安全性的承诺。

#### 1.2 GC改进和内存管理

#### 1.3 线程本地握手

JDK 10将引入一种在线程上执行回调的新方法

#### 1.4 备用内存设备上的堆分配

#### 1.5 其他Unicode语言 - 标记扩展

#### 1.6 基于Java的实验性JIT编译器(一种即时编译工具)

#### 1.7 根证书、根证书颁发认证

#### 1.8 将JDK生态整合单个存储库

#### 1.9 删除工具javah

## 四、java11

#### 1.1 本地变量类型推断

#### 1.2 字符串加强

#### 1.3 集合加强

#### 1.4 Stream 加强

#### 1.5 Optional 加强

#### 1.6 InputStream 加强

#### 1.7 HTTP Client API

#### 1.8 化繁为简，一个命令编译运行源代码

# 参考文档

[Java 8、9、10、11 新特性](<https://blog.csdn.net/lcn_2017/article/details/80146668>)

[Java 8新特性终极指南](<http://www.importnew.com/11908.html#NewFeatureOfLanguage>)

[Java 9 中的 9 个新特性](http://www.importnew.com/24528.html)

[Java 10的新特性](https://blog.csdn.net/bf02jgtrs00xktcx/article/details/79579857)

[Java 11中的 17 个新特性](https://blog.csdn.net/cun_chen/article/details/82807552)