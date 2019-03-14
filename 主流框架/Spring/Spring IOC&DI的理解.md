>摘自：https://jinnianshilongnian.iteye.com/blog/1471944

## IOC
IoC： Inversion of Control，控制反转， 控制权从应用程序转移到框架（如IoC容器），是框架共有特性

###  1 为什么需要IOC容器
#### 1.1 应用程序主动控制对象的实例化及依赖装配
    ```
    A a = new AImpl();  
    B b = new BImpl();  
    a.setB(b);
    ```

本质：创建对象，主动实例化，直接获取依赖，主动装配

缺点：
+ 更换实现需要重新编译源代码
+ 很难更换实现、难于测试
+ 耦合实例生产者和实例消费者

```
A a = AFactory.createA();  
B b = BFactory.createB();  
a.setB(b);  
```
本质：创建对象，被动实例化，间接获取依赖，主动装配  （简单工厂）
缺点:
+ 更换实现需要重新编译源代码
+ 很难更换实现、难于测试

```
A a = Factory.create(“a”);  
B b = Factory.create(“b”);  
a.setB(b);  
```

 <!—配置.properties-->

 ```
a=AImpl  
b=BImpl  
 ```

 本质：创建对象，被动实例化，间接获取依赖， 主动装配
      （工厂+反射+properties配置文件、Service Locator、注册表）

缺点：冗余的依赖装配逻辑

我想直接：
    //返回装配好的a

```
A a = Factory.create(“a”);   
```

#### 1.2 可配置通用工厂：工厂主动控制，应用程序被动接受，控制权从应用程序转移到工厂

```
//返回装配好的a   
A a = Factory.create(“a”);  
```
 <!—配置文件-->

 ```
 <bean id=“a” class=“AImpl”>  
    <property name=“b” ref=“b”/>  
</bean>  
<bean id=“b” class=“BImpl”/>  
 ```

 本质：创建对象和装配对象，
           被动实例化，被动接受依赖，被动装配
         （工厂+反射+xml配置文件）

 缺点：不通用

 步骤：
1. 读取配置文件根据配置文件通过反射
创建AImpl
2. 发现A需要一个类型为B的属性b
3. 到工厂中找名为b的对象，发现没有，读取
配置文件通过反射创建BImpl
4. 将b对象装配到a对象的b属性上

【组件的配置与使用分离开（解耦、更改实现无需修改源代码、易于更好实现） 】

#### 1.3 IoC(控制反转)容器：容器主动控制
```
//返回装配好的a   
A a = ApplicationContext.getBean(“a”);  
```
 <!—配置文件-->

 ```
 <bean id=“a” class=“AImpl”>  
    <property name=“b” ref=“b”/>  
</bean>  
<bean id=“b” class=“BImpl”/>  
 ```

 本质：创建对象和装配对象、管理对象生命周期
          被动实例化，被动接受依赖，被动装配
        （工厂+反射+xml配置文件）

通用：

![common](http://dl.iteye.com/upload/attachment/0065/8544/3374fa35-52bb-35f3-ad03-422dcbc83329.jpg "通用")

**IoC容器：实现了IoC思想的容器就是IoC容器**

###  2 IOC容器特点
1. 无需主动new对象；而是描述对象应该如何被创建即可

    IoC容器帮你创建，即被动实例化；

2. 不需要主动装配对象之间的依赖关系，而是描述需要哪个服务（组件），

    IoC容器会帮你装配（即负责将它们关联在一起），被动接受装配；

3. 主动变被动，好莱坞法则：别打电话给我们，我们会打给你；

4. 迪米特法则（最少知识原则）：不知道依赖的具体实现，只知道需要提供某类服务的对象（面向抽象编程），松散耦合，一个对象应当对其他对象有尽可能少的了解,不和陌生人（实现）说话

5. IoC是一种让服务消费者不直接依赖于服务提供者的组件设计方式，是一种减少类与类之间依赖的设计原则。

###  3 理解IoC容器问题关键：控制的哪些方面被反转了？

1. 谁控制谁？为什么叫反转？ ------ IoC容器控制，而以前是应用程序控制，所以叫反转
2. 控制什么？               ------ 控制应用程序所需要的资源（对象、文件……）
3. 为什么控制？             ------ 解耦组件之间的关系
4. 控制的哪些方面被反转了？ ------ 程序的控制权发生了反转：从应用程序转移到了IoC容器。

思考：

1. IoC/DI等同于工厂吗？
2.  IoC/DI跟以前的方式有什么不一样？

领会：主从换位的思想

![common](http://dl.iteye.com/upload/attachment/0065/8546/47b22d6e-3037-3ab0-9139-8914a91545bf.jpg "通用")

###  4 实现了IoC思想的容器就是轻量级容器吗？

如果仅仅因为使用了控制反转就认为这些轻量级容器与众不同，就好象在说我的轿车与众不同因为它有四个轮子？

容器：提供组件运行环境，管理组件声明周期（不管组件如何创建的以及组件之间关系如何装配的）；

IoC容器不仅仅具有容器的功能，而且还具有一些其他特性---如依赖装配

控制反转概念太广泛，让人迷惑，后来Martin Fowler 提出依赖注入概念
Martin Fowler  Inversion of Control Containers and the Dependency Injection pattern  
http://martinfowler.com/articles/injection.html

## DI

### 什么是DI?

**DI：依赖注入（Dependency Injection） ：用一个单独的对象（装配器）来装配对象之间的依赖关系 。**

![common](http://dl.iteye.com/upload/attachment/0065/8548/7e02c918-3a43-3cdd-a6c3-0920910f8840.jpg "通用")

### 理解DI问题关键

+ 谁依赖于谁？           -------   应用程序依赖于IoC容器
+ 为什么需要依赖？        -------   应用程序依赖于IoC容器装配类之间的关系
+ 依赖什么东西？          -------   依赖了IoC容器的装配功能
+ 谁注入于谁？            -------   IoC容器注入应用程序
+ 注入什么东西？          -------   注入应用程序需要的资源（类之间的关系）

更能描述容器其特点的名字——“依赖注入”（Dependency Injection）

IoC容器应该具有依赖注入功能，因此也可以叫DI容器

### DI优点

1. 帮你看清组件之间的依赖关系，只需要观察依赖注入的机制（setter/构造器），就可以掌握整个依赖（类与类之间的关系）。
2. 组件之间的依赖关系由容器在运行期决定，形象的来说，即由容器动态的将某种依赖关系注入到组件之中。
3. 依赖注入的目标并非为软件系统带来更多的功能，而是为了提升组件重用的概率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不用关心具体的资源来自何处、由谁实现。

使用DI限制：组件和装配器（IoC容器）之间不会有依赖关系，因此组件无法从装配器那里获得更多服务，只能获得配置信息中所提供的那些。

### 实现方式

1. 构造器注入
2. setter注入
3. 接口注入：在接口中定义需要注入的信息，并通过接口完成注入

```
      @Autowired
      public void prepare(MovieCatalog movieCatalog,
          CustomerPreferenceDao customerPreferenceDao) {
          this.movieCatalog = movieCatalog;
          this.customerPreferenceDao = customerPreferenceDao;
      }
```

## 使用IoC/DI容器开发需要改变的思路

1. 应用程序不主动创建对象，但要描述创建它们的方式。
2. 在应用程序代码中不直接进行服务的装配，但要配置文件中描述哪一个组件需要哪一项服务。容器负责将这些装配在一起。

其原理是基于OO设计原则的The Hollywood Principle：Don‘t call us, we’ll call you（别找我，我会来找你的）。也就是说，所有的组件都是被动的（Passive），所有的组件初始化和装配都由容器负责。组件处在一个容器当中，由容器负责管理。
