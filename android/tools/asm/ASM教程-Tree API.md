# ASM教程-Tree API
## 6 类
### 6.1 接口和组件
处理java classes是基于ClassNode类.

#### 6.1.2 生成类
使用TreeApi生成类是创建一个ClassNode,增加他的域和方法.

使用treeAPI要多花费30%的时间并且消耗更多的内存.不过这个使用起来更方便.

#### 6.1.3 增减类成员.
直接操作ClassNode的成员变量就可以了.
### 6.2 组件组成
将表示一个类的字节数组转为组件.
#### 6.2.1 表示
ClasNode也有一个accept方法,接受ClassVisitor(ClassReader).
#### 6.2.2 

## 7. 方法
### 7.1 接口和组件
MethodNode
