### 常见类

#### String对象

**实现**:

**创建的特点:**

1. 通过""创建对象会在运行时常量池开辟内存空间,并赋值
2. 通过new String()方法创建对象,在堆中开辟空间,对象引用指向堆中,堆中对象再指向运行时常量池.

#### Java注解详解

Java注解是一种标注.Java中的类,方法,变量,参数,包等均可以被注解标注从而添加额外信息.

**元注解**

`@Document @Retention @Target @Inherited @Repeatable`等用来注解其他注解的注解称为元注解,共5个

1. `Target`注解用来声明可以用到什么地方,它的枚举字段:

   ```
   TYPE:类,接口,注解,没住
   FILED:字段
   METHOD:方法
   PARAMTER:参数
   CONSTRUCTOR:构造方法
   LOCAL_VARIABLE:本地变量
   ANNOTATION_TYPE:注解
   PACKAGE:包
   TYPE_PARAMETER:类型参数
   ```

2. `@Retention`注解用来声明注解的生命周期,即表明注解会被保留到哪一阶段.枚举字段:

   ```
   SOURCE:保留到源代码阶段,编译时被擦除
   CLASS:保留到类文件阶段.默认声明周期
   RUNTIME:保留到JVM运行阶段
   ```

3. `Documentd` 表示该注解会在javadoc中生成

4. `Inherited` 表明允许子类集成父类该注解

5. `@Repeatable` 表明同一个地方可重复使用.

   ​