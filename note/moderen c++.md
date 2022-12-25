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
#include <iostream>
class A
{
public:
	int* pointer;
public:
	A() :pointer(new int(1)) {
		std::cout << " 构造" << pointer << std::endl;
	};
	A(A& a) :pointer(new int(*a.pointer)) {
		std::cout << " 拷贝" << pointer << std::endl;
	} 
	A(A&& a) :pointer(a.pointer) {
		a.pointer = nullptr;
		std::cout << " 移动" << pointer << std::endl;
	}
	~A() {
		std::cout << " 析构" << pointer << std::endl;
		delete pointer;
	};
};

// 防止编译器优化
A return_rvalue(bool test) {
	A a, b;
	if (test) return a; // 等价于 static_cast<A&&>(a);
	else return b;      // 等价于 static_cast<A&&>(b);
}

int main() {
	A obj = return_rvalue(false);
	std::cout << "obj:" << std::endl;
	std::cout << obj.pointer << std::endl;
	std::cout << *obj.pointer << std::endl;
	return 0;
}
```
	1.首先会在return_rvalue内部构造两个A对象，于是获得两个构造函数的输出
	2.函数返回后，产生一个将亡值，被A的移动构造引用，从而延长生命周期，并将这个右值中的指针拿到，保存到了obj中，而将亡值的指针被设置为nullptr，防止这块内存区域被销毁
- **完美转发**
# 容器
## 线性容器
### std::array
- 为什么要引入std::array而不是std::vector
	std::array大小是固定的；std::vector会自动扩容，如果存入数据后，对数据进行了删除操作，容器不会自动归还内存，需要手动运行shrink_to_fit释放这部分内存
### std::forward_list
- 列表容器，std::forward_list是单向链表实现，提供了O(1)复杂度的元素插入，不支持快速随机访问，不提供size()方法。std::list是双向链表实现。当不需要双向迭代时，std::forward_list比std::list有更高的空间利用率
## 无序容器
- **std::unordered_map/std::unordered_multimap**
- **std::unordered_set/std::unordered_multiset**
### 元组
- 基本操作
	- std::make_tuple 构造元组
	- std::get 获得元组某个位置的值
	- std::tie 元组拆包
- 使用案例
```cpp
#include <tuple>
#include <iostream>

auto get_student(int id)
{
	// 返回类型被推断为 std::tuple<double, char, std::string>
	if (id == 0)
		return std::make_tuple(3.8, 'A', "张三");
	if (id == 1)
		return std::make_tuple(2.9, 'C', "李四");
	if (id == 2)
		return std::make_tuple(11.7, 'D', "王五");
	return std::make_tuple(0.0, 'D', "null");
	// 如果只写0会出现推断错误，编译失败
}

int main()
{
	auto student = get_student(0);
	
	double gpa;
	char grade;
	std::string name;
	
	// 元组进行拆包
	std::tie(gpa, grade, name) = get_student(0);
}
```
# 智能指针和内存管理
## std::shared_ptr
- 使用案例
```cpp
#include <iostream>
#include <memory>
void foo(std::shared_ptr<int> i) {
	(*i)++;
}

int main() {
	auto pointer = std::make_shared<int>(10);
	foo(pointer);
	std::cout << *pointer << std::endl;
	return 0;
}
```
- std::shared_ptr可以通过get()方法来获取原始指针，通过reset()来减少一个引用技术，并通过use_count()来查看一个对象的引用计数
## std::unique_ptr
- 使用案例
```cpp
#include <iostream>
#include <memory>

struct Foo {
	Foo() { std::cout << "Foo::Foo" << std::endl; }
	~Foo() { 
		std::cout << "Foo::~Foo" << std::endl; 
	}
	void foo() {
		std::cout << "Foo::foo" << std::endl;
	}
};

void f(const Foo&) {
	std::cout << "f(const Foo&)" << std::endl;
}

int main() {
	std::unique_ptr<Foo> p1(std::make_unique<Foo>());
	std::unique_ptr<Foo> p2(std::move(p1));
}
```
## std::weak_ptr
- std::weak_ptr是一种弱引用，不会引起引用计数的增加
- 可以用于检查std::shared_ptr是否存在
- expired()方法，在资源未释放时，返回false，否则返回true()
- lock()方法，在原始对象未被释放时，返回指向原始对象的std::shared_ptr指针，否则返回nullptr

# 并行与并发
## 互斥量与临界区
- 使用案例-RAII写法
```cpp
#include <iostream>
#include <mutex>
#include <thread>

int v = 1;

void critical_section(int change_v) {
	static std::mutex mtx;
	std::lock_guard<std::mutex> lock(mtx);
	// 执行竞争操作
	v = change_v;
	// 离开此作用域后mtx会被释放
}

int main() {
	std::thread t1(critical_section, 2), t2(critical_section, 3);
	t1.join();
	t2.join();
	
	std::cout << v << std::endl;
	return 0;
}
```
- 使用案例-手动调用lock和unlock
```cpp
#include <iostream>
#include <mutex>
#include <thread>

int v = 1;

void critical_section(int change_v) {
	static std::mutex mtx;
	std::unique_lock<std::mutex> lock(mtx);
	// 执行竞争操作
	v = change_v;
	std::cout << v << std::endl;
	// 将锁进行释放
	lock.unlock();
	// 再次加锁
	lock.lock();
	v += 1;
	std::cout << v << std::endl;
}

int main() {
	std::thread t1(critical_section, 2), t2(critical_section, 3);
	t1.join();
	t2.join();

	std::cout << v << std::endl;
	return 0;
}
```
## 期物
- 使用案例
```cpp
#include <iostream>
#include <future>
#include <thread>

int main() {
	// 将一个返回值为7的lambda表达式封装到task中
	// std::packaged_task的模板参数为要封装函数的类型
	std::packaged_task<int()> task([]() {return 9; });
	// 获得task的期物
	std::future<int> result = task.get_future(); // 在一个线程中执行task
	std::thread(std::move(task)).detach();
	std::cout << "waiting...";
	result.wait(); // 在此设置屏障，阻塞到期物的完成
	// 输出执行结果
	std::cout << "done!" << std::endl << "future result is "
		<< result.get() << std::endl;
	return 0;
}
```
- 在封装好要调用的目标后，可以使用get_future()来获得一个std::future对象，以便之后实施线程同步