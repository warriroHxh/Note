## 数据类型说明

|数据类型|16位平台|32位平台|64位平台|
|-|-|-|-|
|char|1Byte|1Byte|1Byte|
|pointer|2Byte|4Byte|8Byte|
|short|2Byte|2Byte|2Byte|
|int|2Byte|4Byte|4Byte|
|float|4Byte|4Byte|4Byte|
|double|8Byte|8Byte|8Byte|
|long|4Byte|4Byte|8Byte|
|long long|8Byte|8Byte|8Byte|

### 浮点数存储方式
- float占4个字节，32bits
|符号位|指数位|尾数部分|
|-|-|-|
|1bits|8bits|23bits|
- double占用8个字节，64bits
|符号位|指数位|尾数部分|
|-|-|-|
|1bits|11bits|52bits|

### volatile
指出变量是随时可能发生变化的，每次使用时必须从变量的地址中读取

### const和#define
- const是由编译器处理的，提供类型检查和作用域检查
- 宏定义由预处理器处理，单纯的文本替换

### const修饰类的成员函数
表明函数调用不会对对象做出任何更改

### static关键字
- 全局静态变量
- 局部静态变量
- 静态函数
- 类的静态成员
实现多个对象之间的数据共享
- 类的静态函数
对静态成员的医用不需要用对象名，实现中不能直接引用类中说明的非静态成员

### 指针和引用的本质区别
- 引用是变量的别名，本身不具有单独的内存空间，属于直接访问；指针是指向地址的变量，有单独的内存空间，属于间接访问
- 引用创建时必须初始化，且不能更改绑定；指针可以不初始化，可以更改指向
- sizeof时，指针大小为4Byte，引用则是被引用对象的大小

### 构造函数初始化的顺序
- 基类的静态变量或全局变量
- 派生类的静态变量或全局变量
- 基类的成员变量
- 派生类的成员变量

### 虚函数
- non-vritual函数：不希望派生类重写
- virtual函数：希望派生类重写，且有默认定义
- pure函数：希望派生类必须重写，无默认定义

### new和malloc的区别
- new分配内存按照数据类型进行分配，malloc分配内存按照指定的大小分配
- new 返回指定对象的指针，malloc返回void *
- new分配内存+执行构造函数，malloc只分配内存
- new-delete，malloc-free
- new是操作符，可以重载；malloc是库函数
- new失败，抛出bad_malloc的异常，malloc失败返回NULL

### 虚函数常见问题
#### 1.虚函数表中放什么，子类和父类是否共用一份虚函数表
- 虚函数表属于类，类的所有对象共享这个类的虚函数表
- 不同对象的虚函数表是一样的
- 不同对象的vptr指向的该表中的内容会有所不同
- 子类先从父类复制了一个虚函数表，如果子类重写了父类的虚函数，子类的虚函数标会用子类重写的虚函数地址覆盖父类的，如果没有重写，则使用父类的函数地址
- vptr放在类的起始位置

### c++类型转换
|类型|作用|
|-|-|
|static_cast|静态类型转换|
|reinterpreter_cast|重新解释类型转换|
|dynamic_cast|子类和父类之间的多态类型转换|
|const_cast|去掉const属性转换|

### RTTI
运行阶段类型识别(Runtime Type Identification)

### RAII
资源获取即初始化(Resource Acquisition Is Initializaiton)

### vector的push_back()方法的实现
- push_back是浅拷贝
- 调用过程中，如果长度等于容量，会导致扩容，容量变为之前的两倍
- 新增数据时，会分配一块更大的内存，将原来的数据复制过来，释放之前的内存
- 对vector的任何操作，一旦引起空间重新配置，指向原vector的所有迭代器都会失效

### vector和list，删除元素后指针和迭代器的变化
- vector和list都是左闭右开区间
- 删除末尾元素，返回end()
- 删除中间元素，返回该元素下一个元素的迭代器

### map和set的区别
都是c++的关联容器，底层实现都是红黑树
- map中的元素是key-value，set就是关键字的简单集合
- set的迭代器是const，map允许修改value但是不允许修改key
- map支持下标操作，set不支持下标操作
