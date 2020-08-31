---
typora-root-url: image
---

spring knowledge

1. Class  instance method

```java
//1. use jdk method 
Class<E> clzE;
clzE.newInstance();
//2. use spring beanutils
public class test {
    public static void BeanUtilsTest() {
        Test testA = BeanUtils.instantiate(Test.class);
        System.out.println("insBtantiate  "+testA.toString());
        
        Test testB = BeanUtils.instantiateClass(Test.class);
        System.out.println("instantiateClass  "+testB.toString());
        // TestExtend class must extend Test class  ，else it will has error。 
        Test testC = BeanUtils.instantiateClass( TestExtend.class,Test.class);
        System.out.println("instantiateClass  "+testC.toString());
 
        // use Constructor method
        try {
            Constructor<?> constructor = Test.class.getDeclaredConstructor();
            ReflectionUtils.makeAccessible(constructor);
 
            Object testObj= BeanUtils.instantiateClass(constructor);
            System.out.println(testObj.toString());
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        BeanUtilsTest();
    }
}



```

2 .  Bean Wrapper

beanwrapper 可以用来进行相关额属性获取和设置

```java
Test test = new Test();
BeanWrapper testWrapper = new BeanWrapperImpl(test);
testWrapper.setPropertyValue("name", "jay");
//支持类中嵌套属性
testWrapper.setPropertyValue("company.name", "abc");
Object name = testWrapper.getPropertyValue("name");
//支持类中嵌套属性
Object companyName = testWrapper.getPropertyValue("company.name");
```

3. conversion 类型转换器相关

   spring是通过ConversionService进行类型转换，通过ConverterRegistry进行转换器注册

   其中ConversionService中的方法canConverter判断是否可以转失，convert调用相应的转换器进行类型转换操作

   ConverterRegistry中的方法add(Converter)是注册一个转换器

   具体可以参考 https://blog.csdn.net/u013485533/article/details/47296361

   应用场景 主要用于在数据类型需要进行默认转换的过程中



4.spring  三大器： 过滤器，监视器，拦截器。

三大器的对比：如图

![](/../springknowledge.assets/883340-20170830152048640-620252394.png)

过滤器和拦截器的调用顺序：

![](/../springknowledge.assets/20190326225904854.png)

过滤器：

```txt
通过继承 spring web 中OncePreRequestFilter 来实现过滤器
在通过添加@WebFilter 注解， 然后在application.java启动class 文件加入@ServletComponentSacn注解实现 filter组件的注册。
```

监听器：
```txt
这里我们再补充一下常用的监听器接口： （常见的servlet监听器可以使用@WebListener 和@ServletComponentSacn 直接配置完成）

1.ServletContextListener -- 监听servletContext对象的创建以及销毁

    1.1    contextInitialized(ServletContextEvent arg0)   -- 创建时执行

    1.2    contextDestroyed(ServletContextEvent arg0)  -- 销毁时执行

2.HttpSessionListener  -- 监听session对象的创建以及销毁

    2.2   sessionCreated(HttpSessionEvent se)   -- 创建时执行

    2.2   sessionDestroyed(HttpSessionEvent se) -- 销毁时执行

3.ServletRequestListener -- 监听request对象的创建以及销毁

    3.1    requestInitialized(ServletRequestEvent sre) -- 创建时执行

    3.2    requestDestroyed(ServletRequestEvent sre) -- 销毁时执行

4.ServletContextAttributeListener  -- 监听servletContext对象中属性的改变

    4.1    attributeAdded(ServletContextAttributeEvent event) -- 添加属性时执行

    4.2    attributeReplaced(ServletContextAttributeEvent event) -- 修改属性时执行

    4.3    attributeRemoved(ServletContextAttributeEvent event) -- 删除属性时执行

5.HttpSessionAttributeListener  --监听session对象中属性的改变

    5.1    attributeAdded(HttpSessionBindingEvent event) -- 添加属性时执行

    5.2    attributeReplaced(HttpSessionBindingEvent event) -- 修改属性时执行

    5.3    attributeRemoved(HttpSessionBindingEvent event) -- 删除属性时执行

6.ServletRequestAttributeListener  --监听request对象中属性的改变

    6.1    attributeAdded(ServletRequestAttributeEvent srae) -- 添加属性时执行

    6.2    attributeReplaced(ServletRequestAttributeEvent srae) -- 修改属性时执行

    6.3    attributeRemoved(ServletRequestAttributeEvent srae) -- 删除属性时执行
```

拦截器：

```java
//生成拦截器class 通过引用HandlerInterceptor  接口来生成对应拦截器 class
package interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class Myinterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

```java
//通过配置@Configuration class文件来实现拦截器的注册。
//class 文件需要引用WebMvcConfigurer 接口 然后重写addInterceptors 方法实现相关的注册。
package interceptor;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myinterceptor()).addPathPatterns("/**");
    }
    @Bean
    Myinterceptor myinterceptor(){
        return new Myinterceptor();
    }
}    
```

