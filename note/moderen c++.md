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
