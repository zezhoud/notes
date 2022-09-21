# Java 基础

### 自增自减

在对一个变量做加 1 或减 1 处理时，可以使用自增运算符++ 或自减运算 --。++ 或 -- 是单目运算符，放在操作数的前面或后面都是允许的。++ 与 -- 的作用是使变量的值增 1 或减 1。操作数必须是一个整型或浮点型变量。自增、自减运算的含义及其使用实例如下表所示。

| 运算符 | 含义                                      | 实例                | 结果    |
| :----- | :---------------------------------------- | :------------------ | :------ |
| i++    | 将 i 的值先使用再加 1 赋值给 i 变量本身   | int i=1; int j=i++; | i=2 j=1 |
| ++i    | 将 i 的值先加 1 赋值给变量 i 本身后再使用 | int i=1; int j=++i; | i=2 j=2 |
| i--    | 将 i 的值先使用再减 1 赋值给变量 i 本身   | int i=1; int j=i--; | i=0 j=1 |
| --i    | 将 i 的值先减 1 后赋值给变量 i 本身再使用 | int i=1; int j=--i; | i=0 j=0 |

-   自增/自减只能作用于变量，不允许对常量、表达式或其他类型的变量进行操作。常见的错误是试图将自增或自减运算符用于非简单变量表达式中。
-   自增/自减运算可以用于整数类型 byte、short、int、long，浮点类型 float、double，以及字符串类型 char。
-   在 Java1.5 以上版本中，自增/自减运算可以用于基本类型对应的包装器类 Byte、Short、Integer、Long、Float、Double 和 Character。
-   自增/自减运算结果的类型与被运算的变量类型相同。

```java
int i = 1;
int j = i++;
int k = i + ++i*i++;
// i = 4 j = 1 k = 11

int i = 2;
int j = i++ * ++i + ++i * i++;
// i = 6 j = 33
```

对于复杂的自增自减运算

*   赋值=，最后计算
*   =右边的从左到右加载值依次压入操作数栈
*   实际先算哪个根据运算符优先级确定
*   自增、自减操作都是直接修改变量的值，不经过操作数栈
*   最后的赋值前，临时结果也是存储在操作数栈中

### 单例模式（singleton）

单例模式是软件开发中常用的设计模式之一，即某个类在整个系统中只能有一个实例对象可被获取和使用的代码模式。

**饿汉式**

直接创建对象，不会存在线程安全问题

直接实例化饿汉式

```java
/**
 * 1.构造器私有化
 * 2.自行创建，并用静态变量保存
 * 3.向外提供这个实例
 * 4.强调这是一个单例，用final修改
 */
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();
    private Singleton() {
    }
}

```

枚举式

```java
/**
 * 枚举类型：表示该类型的对象是有限的几个
 * 可以限定为一个，成为了单例
 */
public enum Singleton {
    INSTANCE
}
```

静态代码块

```java
public class Singleton {
    private static final Singleton INSTANCE;
    private Integer id;

    static {
        // 可以在此处编写参数的初始化代码
        INSTANCE = new Singleton(...);
    }

    private Singleton(Integer id) {
        this.id = id;
    }
}
```

**懒汉式**

延迟创建对象

线程不安全

```java
/**
 * 1.构造器私有化
 * 2.用一个静态变量保存这个唯一的实例
 * 3.提供一个静态方法，获取这个实例对象
 */
public class Singleton {
    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

线程安全

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

使用同步代码块，将代码多线程不安全的问题解决

静态内部类

```java
/**
 * 在内部类被加载和初始化时，才创建INSTANCE实例对象
 * 静态内部类不会随着外部类的加载和初始化而创建，是需要单独加载和初始化的
 * 因为在内部类加载和初始化时，创建的，因此线程是安全的
 */
public class Singleton {
    private Singleton() {
    }

    private static class Inner {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Inner.INSTANCE;
    }
}
```

### 类初始化过程

1.   一个类要创建实例需要先加载并初始化该类
     *   main方法所在的类需要先加载和初始化
2.   一个子类要初始化需要先初始化父类
3.   一个类初始化就是执行`<clinit>()`方法
     *   `<clinit>()`方法由静态类变量显示赋值代码和静态代码块组成
     *   类变量显示赋值代码和静态代码块从上到下顺序执行
     *   `<clinit>()`只执行一次

### 实例化初始化过程

实例初始化就是执行`<init>()`方法

*   `<init>()`可能重载有多个，有几个构造器就有几个`<init>()`方法
*   `<init>()`方法由非静态实例变量显示赋值代码和非静态代码块、对应构造器代码组成
*   非静态实例变量显示复制代码和非静态代码块代码从上倒下顺序执行，而对应构造器的代码最后执行
*   每次创建实例对象，调用对于构造器，执行的就是对应的`<init>()`方法
*   `<init>()`方法的首行是`super()`，即对应父类的`<init>()`方法

### 初始化顺序

存在继承的情况下，初始化顺序为：

-   父类（静态变量、静态语句块）
-   子类（静态变量、静态语句块）
-   父类（实例变量、普通语句块）
-   父类（构造函数）
-   子类（实例变量、普通语句块）
-   子类（构造函数）