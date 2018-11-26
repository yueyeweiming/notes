# ASM教程
## 1. 介绍
#### ASM的结构
1. 事件生产者(class parser)
2. 预先定义的事件过滤器
3. 事件消费者 class writer

##### 使用API分两步
1. 构建事件生产者,filter和消费者组件组成复杂的结构
2. 启动生产者,执行转换进程.

### 1.3 结构
1. org.objectweb.asm[.signature]定义了基于事件的API以及parser和writer,他们位于asm.jar中
2. org.objectweb.asm.util 提供基于core API的多种工具,位于asm-util.jar中国年
3. org.objectweb.asm.commons 提供了几个预定义的transformers,大部分是基于coreAPI,在asm-commons.jar中
4. org.objectweb.asm.tree ,定义了tree API,提供了core API和treeAPI之间的转换工具,位于asm-tree.jar中
5. org.objectweb.asm..tree.analysis 包提供了一个基于tree api的class分析框架和几个预先定义好的class分析器,位于asm-analysis.jar中./Users/magic/work/meituan/mtblog/posts/waimai_android_annotation_asm.md

文档分两部分,前一部分是coreAPI,第二部分是treeAPI.

# coreAPI部分
## 2 类
这一章是讲述如何使用core API生成和转换已编译的java 类.
### 2.1 结构
#### 2.1.1 总览
class文件结构包括:

1. 类的修饰符(比如public或private),名称,父类,接口和注解的描述的部分
2. 类的field声明,每一部分都包括修饰符,名称,类型和field的注解
3. 类中声明的方法和构造器,描述了修饰符,名称,返回值和参数类型和方法注解以及以java字节码命令存在的方法体.

#### 2.1.2 内部名称
类型在类中的表示方式,主要是类和接口.表示方式是类的名称(包括包名),用`/`替换了`.`,例如String用内部名称表示就是java/lang/String.

#### 2.1.3 类型描述符
field的描述是使用类型描述符,原始类型是一个字符如下表,class的类型是L+内部类型的格式,数组类型是[+数组元素类型的类型描述符.

java类型|类型描述符
-----|----
boolean|Z
char|C
byte|B
int|I
float|F
long|J
double|D
Object|Ljava/lang/Object
int[]|[i
Object[][]|[[Ljava/lang/Object


#### 2.1.4 方法描述符
用一个字符串描述了方法的一组参数类型和返回值的类型描述符,格式为:
左括号+参数类型+有括号+返回值类型(V代表void).
方法描述符不包含方法名和参数名

### 2.2 接口和组件
#### 2.2.1 表示
生成和转换class是基于ClassVisitor抽象类.这个类中的方法都代表同名的class文件结构的部分,每一不分的访问函数携带的参数描述了他们的内容.

##### ClassVisitor方法的调用顺序必须符合以下

```
visit visitSource?visitOuterClass?(visitAnnotation|visitAttribute)*(visitInnerClass|visitField|visitMethod)* visitEnd
```

asm提供了3个基于ClassVisitor API的组件来生成和转换classes

1. Classreader,事件产生着
2. ClassWriter ClassVisitor的子类,用二进制的方式构建class,事件消费者
3. ClassVisitor: 代理了从另一个ClassVisitor实例接收到的方法,事件过滤器.

#### 2.2.2 解析类
解析类只需要ClassReader

1. 定义一个ClassVisitor的子类,实现对应的方法
2. 将ClassVisitor和ClassReader关联起来,接收ClassReader读到的数据,示例如下

```
ClassPrinter cp = new ClassPrinter();
ClassReader cr = new ClassReader("java.lang.Runnable");
cr.accept(cp, 0);
```

##### ClassReader的构造方式
构造器接收一个类,这个类可以用名称(例如java.lang.Runnable),或者值,或者字节数组,或者InputStream的形式.

#### 2.2.3 生成类
生成类只需要ClassWriter

### ClassVisitor中的几个命名
* access:java中的修饰符
* name: 名称,比如类名,方法名
* signature 类型.
* supername: 父类名称
*  interfaces:接口名称数组
*  desc: field的初始化代码


## 3. 方法Method
这章是讲怎么使用core API来生成和转换方法.
### 3.1 结构
在class文件中,methods的代码被存储为一序列的二进制命令,了解这些指令怎么工作是必备信息,这章是简介,想要详细的了解,需要阅读JVM规范.

#### 3.1.1 执行模型
Java代码运行在线程中,每个线程都有自己的方法栈,由栈桢组成.每个栈桢包括两部分,本地变量和操作栈不分,本地变量部分可以用任意的顺序通过索引访问,操作栈部分用于通过字节命令作为操作符.

#### 3.1.2 二进制命令
二进制命令有一个命令标识符(opcode)和固定数量的参数组成

* opcode 是一个无符号byte值,有助记符,不如0被表示为NOP,表示什么都不做
* 参数是清晰定义行为的静态值,例如,GOTO label命令,参数是lable,代表下一条要执行的命令.参数是被静态的存储在class中,而操作栈中的操作的值是在运行时才能知道.

1. ILOAD(boolean,byte,char,short,int),LLOAD.FLOAD,DLOAD,ALOAD(非原始类型) 读取本地变量,把它的值压入操作栈中,参数是索引,类似的,ISTORE,LSTORE,,FSTORE,DSTORE,ASTORE从操作栈中pop一个值,通过索引的参数i把它存储在一个本地变量中.

xLoad和xStore是分类型的(几乎所有的类型都是分类型的).

##### 命令解析
##### 栈命令
用来操作栈中的值

* POP从栈中pop出值
* DUP 栈顶值复制一份,push入栈.
* SWAP pop两个值,交换他们顺序之后再push入栈






















