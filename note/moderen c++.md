### nullptr
- c++11的新特性。
- 传统的c++会将 NULL定义为 0，且不同的编译器对NULL的处理存在差异，导致存在形如下列函数定义时，可能由于隐式类型转换，会出现执行func(NULL)时，会去调用void func(int a)这样违反直觉的调用
```cpp
void func(int a);
void func(char * a);
```
### constexpr
- 常量表达式，显示声明函数或构造对象在编译器会成为常量表达式

### std::initializer_list
- 初始化列表
- 例子
```cpp
class MagicFoo{
public:
    std::vector<int> vec;
    MagicFoo(std::initializer_list<int> list) {
        for(std::initializer_list<int>::iterator iter = list.begin();
            iter != list.end(); ++iter)
            vec.push_back(*iter);
    }
}
```
### decltype
- 解决*auto*关键字只能对变量进行类型推导的问题，用法同*sizeof*
```
decltype(表达式)
```
### 区间for迭代
- 例子
```cpp
#include <iostream>  
#include <vector>  
#include <algorithm>  
int main(){  
    std::vector<int> vec = {1, 2, 3, 4, 5};  
    for(auto iter : vec){  // read only
        std::cout << iter << std::endl;  
    }  
    for(auto &iter : vec){  // writebale
        std::cout << iter << std::endl;  
    }  
}
```
### 显式虚函数重载
- **override** 显式的告知编译器进行重载
- **final** 防止类被继续继承，以及虚函数继续重载
### 显示禁用默认函数
```cpp
class Magic {
    public:
        Magic() = default; // 显式声明使用编译器生成的构造函数
        Magic& operater=(const Magic&) = delete; // 显示声明拒绝编译器生成构造
}
```
### Lambda表达式
- 基本语法
```
[捕获列表](参数列表) mutable(可选) 异常属性 -> 返回类型 {
    // 函数体
}
```
- 捕获类型
    - 值捕获
    - 引用捕获
    - 隐式捕获
        - [] 空捕获列表
        - [name1, name2, ...] 捕获一系列变量
        - [&]引用捕获
        - [=]值捕获
    - 表达式捕获(c++14新特性)
### 函数对象包装器
- std::function
- std::bind和std::placeholders
    - 例子
```cpp
int foo(int a, int b, int c) {
    ;
}
int main() {
    // 将参数1， 2绑定到函数foo上
    // 但使用std::placeholders::_1来对第一个参数进行占位
    auto bindFoo = std::bind(foo, std::placeholders::_1, 1, 2);
    bindFoo(1);
}
```
### 右值引用
- **左值** 赋值符号左边的值，表达式后依然存在的持久对象
- **右值** 表达式结束后就不再存在的临时对象
- **纯右值** 
    - 纯粹的字面量，如10，true
    - 求值结果相当于字面量或匿名临时变量，如 1+2
    - 非引用返回的临时变量，运算表达式产生的临时变量，原始字面量，Lambda表达式
    - 字符串字面量是一个左值，类型为const char数组
- **将亡值** 即将被销毁，却能被移动的值
```cpp
std::vector<int> foo() {
    std::vector<int> temp = {1, 2, 3, 4};
    return temp;
}
std::vector<int> v = foo();
```
    最后一行中，v是左值，foo()返回的值就是右值，也是纯右值。但是v可以被别的变量捕获到，而foo()产生的返回值作为一个临时变量，一旦被v复制后，将立即被销毁，无法获取，也不能被修改。
- **右值引用和左值引用**
- **移动语义**
    - 可以避免无意义的拷贝构造，加强了性能
```cpp
#include
```