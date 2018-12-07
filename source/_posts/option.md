---
title: Java积极使用optional替代null
date: 2018-07-02 14:30:00
tags:  java
       编程语言
categories: 编程语言 
---
  Java中有值类型和引用类型，null是引用类型的默认值，是对于不确定指针的建模，正常情况下，堆中的对象解引用调用，当声明的变量不指向任何堆中的对象时，此时调用任何方法都将引发过NullPointerException，因此在Java对象调用时，理论上必须要判断此对象为空（````if(object!=null)```）,但由于大多数业务上要求，根据上下文，对象在大部分情况下不为空，即便如此，在Java代码中，调用边界系统api,对于入参的非空判断依然占据了相当长的行数。那有没有更好的建模呢？Java8的option<T>提供了更好的解决方案。在Java 8里有一个Optional<T>类，如果你能一致地使用它的话，就可以帮助你避免出现 NullPointer异常。它是一个容器对象，可以包含，也可以不包含一个值。Optional<T>中有 方法来明确处理值不存在的情况，这样就可以避免NullPointer异常了。换句话说，它使用类 型系统，允许你表明我们知道一个变量可能会没有值。

### Optional的方法 

|方法|描述|
|-|-|
|empty |返回一个空的 Optional 实例|
|filter|如果值存在并且满足提供的词，就返回包含该值的 Optional 对象；否则返回一个空的 Optional 对象|
|flatMap| 如果值存在，就对该值执行提供的 mapping函数调用，返回一个 Optional 类型的值，否则就返 回一个空的Optional 对象 
|get| 如果该值存在，将该值用 Optional 封装返回，否则出一个 NoSuchElementException 异常 |
|ifPresent| 如果值存在，就执行使用该值的方法调用，否则什么也不做| 
|isPresent| 如果值存在就返回 true，否则返回 false |
|map |如果值存在，就对该值执行提供的 mapping函数调用| 
|of| 将指定值用 Optional 封装之后返回，如果该值为 null，则出一个 NullPointerException 异常 |ofNullable| 将指定值用 Optional 封装之后返回，如果该值为 null，则返回一个空的 Optional 对象 
|orElse| 如果有值则将其返回，否则返回一个认值 orElseGet 如果有值则将其返回，否则返回一个由指定的 Supplier 接口生成的值 
|orElseThrow| 如果有值则将其返回，否则出一个由指定的 Supplier 接口生成的异常
### 创建optional对象
1.  创建空Optional
    ```java
    Optional<Object> empty = Optional.empty();
    ```
 1. 据一个空值创建Optional 
    ```java
    Optional<Car> optCar = Optional.of(car); 
    ```
1. 可接null的Optional 
    ```java
    Optional<Person> optperson = Optional.ofNullable(person); 
    ```
### 实践
-  使用Optional定义数据模型
    ```java
    public class Person {
        private Optional<Car> car;

        public Optional<Car> getCar() {
            return car;
        }
    }
    ``` 
- 注意，上述字段并不能序列化，对于要序列化的数据模型，可改写成如下
    ```java
    class Person {
        private Car car;

        public Optional<Car> getCarAsOptional() {
            return Optional.ofNullable(car);
        }
    }
    ```
- 重构以下常见代码
    ```java
    public String getName(Person p){
            if (p!=null){
                return p.gerName();
            }
            return null;
        }
    ```
    ```java
    public String getNameOpt(Person p){
            return Optional.ofNullable(p)
                .map(v->v.gerName())
                .orElse("");
        }
    ```
使用option重构后的代码可以进行链式调用了，相当简洁，查看Java8新增的流api,比如map,fiter其返回值都是optional这种null类型安全的
### 总结
- null引用在历上被引入到程序设计语言中，目的是为了表示变量值的缺失。 
- Java 8中引入了一个新的类java.util.Optional<T>，对存在或缺失的变量值进行 建模。
- 你可以使用静态工厂方法Optional.empty、Optional.of以及Optional.ofNull- able创建Optional对象。 
- Optional类支持多种方法，比如map、flatMap、filter，它们在概念上与Stream类 中对应的方法分相似。 
- 使用Optional会使你更积极地解引用Optional对象，以应对变量值缺失的问题， 最终，你能更有效地防止代码中出现不期而至的空指针异常。 
- 使用Optional能帮助你设计更好的API，用户只需要读方法签名，就能了解该方法是 否接受一个Optional类型的值。 