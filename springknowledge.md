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

   ```java
   
   ```

   
