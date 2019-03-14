# Spring AOP 的实现机制

> [来源：大名Dean鼎](www.deanwangpro.com/2017/02/08/aop-in-java/)


AOP（Aspect Orient Programming），一般称为面向切面编程，作为面向对象的一种补充，用于处理系统中分布于各个模块的横切关注点，比如事务管理、日志、缓存等等。AOP实现的关键在于AOP框架自动创建的AOP代理，AOP代理主要分为静态代理和动态代理，静态代理的代表为AspectJ；而动态代理则以Spring AOP为代表。静态代理是编译期实现，动态代理是运行期实现，可想而知前者拥有更好的性能。本文主要介绍Spring AOP的两种代理实现机制，JDK动态代理和CGLIB动态代理。

静态代理是编译阶段生成AOP代理类，也就是说生成的字节码就织入了增强后的AOP对象；动态代理则不会修改字节码，而是在内存中临时生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。

Spring AOP中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理。JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是InvocationHandler接口和Proxy类。

如果目标类没有实现接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意，CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的，诸如private的方法也是不可以作为切面的。

我们分别通过实例来研究AOP的具体实现。

## 直接使用Spring AOP


首先定义需要切入的接口和实现。为了简单起见，定义一个Speakable接口和一个具体的实现类，只有两个方法sayHi()和sayBye()。

**Speakable.java**
```
package com.hongjiaoliu.learning.aop.service;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:41
 */

public interface Speakable {
	void sayHi();

	void sayBye();
}

```

**PersonSpring.java**

```
package com.hongjiaoliu.learning.aop.service;

import org.springframework.stereotype.Service;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:59
 */
@Service
public class PersonSpring implements Speakable {
	public void sayHi() {
		try {
			Thread.currentThread().sleep(30);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Hi!!");
	}

	public void sayBye() {
		try {
			Thread.currentThread().sleep(10);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Bye!!");
	}
}

```

接下来我们希望实现一个记录sayHi()和sayBye()执行时间的功能。

定义一个MethodMonitor类用来记录Method执行时间

**MethodMonitor.java**

```
package com.hongjiaoliu.learning.aop.monitor;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:45
 */

public class MethodMonitor {
	private long start;
	private String method;

	public MethodMonitor(String method) {
		this.method = method;
		System.out.println("begin monitor..");
		this.start = System.currentTimeMillis();
	}

	public void log() {
		long elapsedTime = System.currentTimeMillis() - start;
		System.out.println("end monitor..");
		System.out.println("Method: " + method + ", execution time: " + elapsedTime + " milliseconds.");
	}
}

```
接下来利用本地线程类写一个静态调用类,这样写的好处是如果一个业务流程很长，遍布好多方法，
上游方法不用一直传递MethodMonitor类用于计时

 **MonitorSession.java**

```
package com.hongjiaoliu.learning.aop.monitor;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:52
 */

public class MonitorSession {
	private static ThreadLocal<MethodMonitor> monitorThreadLocal = new ThreadLocal<>();

	public static void begin(String method) {
		MethodMonitor logger = new MethodMonitor(method);
		monitorThreadLocal.set(logger);
	}

	public static void end() {
		MethodMonitor logger = monitorThreadLocal.get();
		logger.log();
	}
}

```

**重中之重，切面的编码类，MonitorAdvice.java**

```
package com.hongjiaoliu.learning.aop.annotation;

import com.hongjiaoliu.learning.aop.monitor.MonitorSession;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:55
 */
@Aspect
@Component
public class MonitorAdvice {

	@Pointcut("execution (* com.hongjiaoliu.learning.aop.service.Speakable.*(..))")
	public void pointcut() {
	}

	@Around("pointcut()")
	public void around(ProceedingJoinPoint pjp) throws Throwable {
		MonitorSession.begin(pjp.getSignature().getName());
		pjp.proceed();
		MonitorSession.end();
	}
}

```

## JDK动态代理

刚刚的例子其实内部实现机制就是JDK动态代理，因为Person实现了一个接口。

为了不和第一个例子冲突，我们再定义一个Person来实现Speakable, 这个实现是不带Spring Annotation的，所以他不会被Spring托管。

**PersonImpl.java**

```
package com.hongjiaoliu.learning.aop.service;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:59
 */

public class PersonImpl implements Speakable {
	public void sayHi() {
		try {
			Thread.currentThread().sleep(30);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Hi!!");
	}

	public void sayBye() {
		try {
			Thread.currentThread().sleep(10);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Bye!!");
	}
}

```

重头戏来了，我们需要利用InvocationHandler实现一个代理，让它去包含Person这个对象。那么再运行期实际上是执行这个代理的方法，然后代理再去执行真正的方法。所以我们得以在执行真正方法的前后做一些手脚。JDK动态代理是利用反射实现，直接看代码。

**DynamicProxy.java**

```
package com.hongjiaoliu.learning.aop.jdkproxy;

import com.hongjiaoliu.learning.aop.monitor.MonitorSession;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是InvocationHandler接口和Proxy类。
 *
 * @author liuhongjao
 * @date 2019/3/13 14:43
 */

public class DynamicProxy implements InvocationHandler {
	private Object target;

	public DynamicProxy(Object object) {
		this.target = object;
	}

	@Override
	public Object invoke(Object arg0, Method arg1, Object[] arg2)
			throws Throwable {
		MonitorSession.begin(arg1.getName());
		Object obj = arg1.invoke(target, arg2);
		MonitorSession.end();
		return obj;
	}

	@SuppressWarnings("unchecked")
	public <T> T getProxy() {
		return (T) Proxy.newProxyInstance(
				target.getClass().getClassLoader(),
				target.getClass().getInterfaces(),
				this
		);
	}
}

```
通过getProxy可以得到这个代理对象，invoke就是具体的执行方法，可以看到我们在执行每个真正的方法前后都加了Monitor。

我实现了一个工厂类来获取Person代理对象

**PersonProxyFactory.java**

```
package com.hongjiaoliu.learning.aop.service;

import com.hongjiaoliu.learning.aop.cglib.CGLibProxy;
import com.hongjiaoliu.learning.aop.jdkproxy.DynamicProxy;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:42
 */

public class PersonProxyFactory {
	public static Speakable newJdkProxy() {
		DynamicProxy dynamicProxy = new DynamicProxy(new PersonImpl());
		Speakable proxy = dynamicProxy.getProxy();
		return proxy;
	}

	public static Person newCglibProxy() {
		CGLibProxy cglibProxy = CGLibProxy.getInstance();
		Person proxy = cglibProxy.getProxy(Person.class);
		return proxy;
	}
}

```

## CGLib动态代理

我们再新建一个Person来，这次不实现任何接口

**Person.java**

```
package com.hongjiaoliu.learning.aop.service;

/**
 * @author liuhongjao
 * @date 2019/3/13 14:58
 */

public class Person {
	public void sayHi() {
		try {
			Thread.currentThread().sleep(30);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Hi!!");
	}

	public void sayBye() {
		try {
			Thread.currentThread().sleep(10);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		System.out.println("Bye!!");
	}
}

```

如果Spring识别到所代理的类没有实现Interface，那么就会使用CGLib来创建动态代理，原理实际上成为所代理类的子类。

**CGLibProxy.java**

```
package com.hongjiaoliu.learning.aop.cglib;

import com.hongjiaoliu.learning.aop.monitor.MonitorSession;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * 如果目标类没有实现接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），
 * 是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意，CGLIB是通过继承的方式做的动态代理，
 * 因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的，诸如private的方法也是不可以作为切面的。
 *
 * @author liuhongjao
 * @date 2019/3/13 15:00
 */

public class CGLibProxy implements MethodInterceptor {
	private static CGLibProxy instance = new CGLibProxy();

	private CGLibProxy() {
	}

	public static CGLibProxy getInstance() {
		return instance;
	}

	private Enhancer enhancer = new Enhancer();

	@SuppressWarnings("unchecked")
	public <T> T getProxy(Class<T> clazz) {
		enhancer.setSuperclass(clazz);
		enhancer.setCallback(this);
		return (T) enhancer.create();
	}

	public Object intercept(Object arg0, Method arg1, Object[] arg2,
							MethodProxy arg3) throws Throwable {
		MonitorSession.begin(arg1.getName());
		Object obj = arg3.invokeSuper(arg0, arg2);
		MonitorSession.end();
		return obj;
	}
}

```

类似的通过getProxy可以得到这个代理对象，intercept就是具体的执行方法，可以看到我们在执行每个真正的方法前后都加了Monitor。

在工厂类中增加获得Person代理类的方法,

```
public static Person newCglibProxy() {
    CGLibProxy cglibProxy = CGLibProxy.getInstance();
    Person proxy = cglibProxy.getProxy(Person.class);
    return proxy;
}

```

## 单元测试类

**TestSpringAop.java**

```
package com.hongjiaoliu.learning.aop;

import com.hongjiaoliu.learning.aop.service.Person;
import com.hongjiaoliu.learning.aop.service.PersonProxyFactory;
import com.hongjiaoliu.learning.aop.service.Speakable;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

/**
 * 单元测试类
 * @author liuhongjao
 * @date 2019/3/13 16:40
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class TestSpringAop {

	@Autowired
	private Speakable personSpring;

	@Test
	public void testSpringStyle(){
		System.out.println("******** spring aop ******** ");
		personSpring.sayHi();
		personSpring.sayBye();
	}

	@Test
	public void testjdkDynamicProxy(){
		System.out.println("******** jdk dynamic proxy ******** ");
		Speakable jdkProxy = PersonProxyFactory.newJdkProxy();
		jdkProxy.sayHi();
		jdkProxy.sayBye();
	}

	@Test
	public void testcglibDynamicProxy(){
		System.out.println("******** cglib proxy ******** ");
		Person cglibProxy = PersonProxyFactory.newCglibProxy();
		cglibProxy.sayHi();
		cglibProxy.sayBye();
	}


}
```

**输出**

```
******** cglib proxy ********
begin monitor..
Hi!!
end monitor..
Method: sayHi, execution time: 39 milliseconds.
begin monitor..
Bye!!
end monitor..
Method: sayBye, execution time: 11 milliseconds.

******** spring aop ********
begin monitor..
Hi!!
end monitor..
Method: sayHi, execution time: 31 milliseconds.
begin monitor..
Bye!!
end monitor..
Method: sayBye, execution time: 12 milliseconds.

******** jdk dynamic proxy ********
begin monitor..
Hi!!
end monitor..
Method: sayHi, execution time: 31 milliseconds.
begin monitor..
Bye!!
end monitor..
Method: sayBye, execution time: 11 milliseconds.
```

## 小结

对比JDK动态代理和CGLib代理，在实际使用中发现CGLib在创建代理对象时所花费的时间却比JDK动态代理要长，实测数据

```
cglib
Method: sayHi, execution time: 39 milliseconds.
jdk
Method: sayHi, execution time: 31 milliseconds.
```
所以CGLib更适合代理不需要频繁实例化的类。

文中代码可以通过GitHub中获取
> https://github.com/hongjiaoliu/course-spring-aop.git
