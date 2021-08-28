# 1基础部分
## 1.1基本语言
### 1.1.1 变量
1. signed char和unsigned char适用于小整数值 ，普通的char用于一个字符串字面值的每个元素的类型或用于与整数的数据相对的字符数据（其中符合没有意义）
2. 定义用于为变量分配存储空间，还可以为变量指定初始值。在一个程序中，变量有且仅有一个定义。  
   声明用于向程序声明变量的类型和名字。  
   定义也是一种声明
   当extern声明有初始化式也可以被当做是定义。  
   变量有三要素：存储空间、类型和名字。  
   e.g. extern double pi = 3.1314  
   同样分配并初始化了存储空间，只有当extern声明位于函数外部时，才可以有初始化式
3. 变量名都是内存地址，
   int a;       
   int *b=&a;  //&表示取出a表示的内存地址，而b表示存储的值是个int类型的地址值

---
### 1.1.2 指针
1. C在编译时，每一个函数都有一个入口地址，该入口地址就是函数指针所指向一个具体的函数。这正如用指针变量可指向整型变量、字符型、数组一样，这里是指向函数。
2. 指向函数的指针一大用处是做回调函数
```c++
int a; //a即是整型
int *p; //*p即是整型-->p即指向整型的指针
int (*p)(int);//(*p)(int)即是整型-->(*p)即是返回整型的函数-->p即是指向这个函数的指针
指向函数指针的指针的正确写法：
int (**p)(int);//(**p)(int)即是整型-->(**P)即是返回整型的函数-->(*p)即是指向这个函数的指针-->p即是指向函数指针的指针
指向函数指针的指针的错误写法即分析：
int *(*p)(int);//*(*p)(int)即是整型-->(*p)(int)即是指向这个整型的指针-->(*p)即是返回这个指针的函数-->p即是指向这个函数的指针
int **p(int);//**p(int)即是整型-->*p(int)即是指向这个整型的指针-->p(int)即是指向这个指针的指针-->p即是返回指针的指针的函数
int (*ff(int)(int*,int);//(*ff(int)(int*,int）)即是整型---->（*ff(int))即是返回整型的函数-->ff(int)是指向函数的指针-->ff是返回指针的函数
----------------------如何更好的定义指向函数指针的指针-----------------
typedef int fuc(int)    //把fuc声明为返回int参数为int的函数类型
typedef fuc* fuc_ptr     //把fuc_ptr声明为指向函数fuc的指针类型
typedef fuc_ptr* fuc_ptr_ptr  //把fuc_ptr_ptr声明为指向函数指针fuc_ptr的指针类型
fuc_ptr_ptr p   //p为指向函数指针的指针
```


---
### 1.1.3 ::范围解析运算符
**分类**

1. 全局作用域符（::name）:用于类型名称（类、类成员、成员函数、变量等）前，表示作用域为全局命名空间
2. 类作用（class::name）：用于表示指定类型的作用域范围是具体某个类的
3. 命名空间作用域符（namespace::name）：用于表示制定类型的作用域范围是具体某个命名空间的


---
### 1.1.4 using
**using 指示**
```c++
using namespace std;
```

**using 声明**
```c++
using std::cin;
```
> 尽量使用声明而不是指示，因为指示会污染命名空间

---
### 1.1.5函数
1. fork函数：在早期的Unix系统中，创建进程比较原始。当调用fork时，内核会把所有的内部数据结构复制一份，复制进程的页表项，然后把父进程的地址空间中的内容逐页的复制到子进程的地址空间中。但从内核角度来说，逐页的复制方式是十分耗时的。现代的UNIX系统采取了更多的优化，例如LINUX，采用了写时复制（COW），而不是对父进程空间进程整体的复制
2. 重载和重写的区别：
**重载**
> 原理：源文件通过编译后，将相同函数名，按照一定的格式，改变成可以区分的，去除了函数在调用时的二义性，从而实现函数的重载。
> 定义：函数名相同，函数的参数个数、参数类型或参数顺序三者中必须至少有一种不同。函数返回值的类型可以相同，也可以不相同。发生在一个类内部，不能跨作用域。

**重写**
> 定义：也叫做覆盖，一般发生在子类和父类继承关系之间。子类重新定义父类中有相同名称和参数的虚函数。(override)

**重定义**
> 定义：也叫做隐藏，子类重新定义父类中有相同名称的非虚函数 ( 参数列表可以不同 ) ，指派生类的函数屏蔽了与其同名的基类函数。可以理解成发生在继承中的重载。
3. strcpy是字符串拷贝函数，从src逐字节拷贝到dest，直到遇到'\0'结束，因为没有指定长度，可能会导致拷贝越界，造成缓冲区溢出漏洞，安全版本是strncpy函数。
4. strlen函数是计算字符串长度的函数，返回从开始到'\0'之间的字符个数。
5. 请你写个函数在main函数执行前执行
```c++
__attribute((constructor))void before()
{
	printf("before main\n");
}
```
6. C语言调用函数顺序：每个函数调用都会分配函数栈。在栈内进行函数执行过程。调用前，先把返回地址压栈，然后把当前函数的esp指针压栈。函数中参数的压栈顺序是从右到左。

---
### 1.1.6 宏定义
1. 
Q:用宏定义定义一年中有多少秒
A:
```c++
#define SECOND_PER_YEAR (60*60*24*365)UL
```
**U NEED KNOW**
- 要懂得预处理器将为你计算常熟表达式的值，比如这里不能直接写31536000
- 考虑这个表达式的值范围，会不会超过int，这里用UL标识为无符号长整型

----
2.
Q:写一个标准宏MIN。这个宏输入两个参数并返回较小的一个
A:

```C++
#define MIN(A,B) ((A)>(B)?(B):(A))
```
**U NEED KNOW**
- 定义函数时一定要记得括号保护变量
2. 在头文件中用保护宏可以避免头文件被多次包含造成的编译错误
```c++
//protect_test.h
#ifndef __PROTECT_TEST_H  
#define __PROTECT_TEST_H
#define SUCCESS 1 
#define FAILED 0 
/* coding here*/
#endif /*__PROTECT_TEST_H*/
```
---
### 1.1.7 const
#### 作用
> 修饰变量，说明该变量不可以被改变
> 修饰指针，分为指向常量的指针和指针常量
> 常量引用，经常用于形参类型，即避免了拷贝，又避免了函数对值的修改
> 修饰成员函数，说明该成员函数内不能修改成员变量
---
1. const与define区别？
A：“const常量具有类型，编译器可以进行安全检查；~~#define宏定义没有数据类型，只是简单的字符串替换~~，不能进行安全检查”

> 这个说法是错的，#define定义的宏变量同样存在类型，与字面值常量的类型相同。
> 正确答案：编译器不会将宏定义加入到符号列表中。

```c++
#define TEST 0  //int型
#define TEST2 128UL   //unsigned long型
#define TEST3 "hello"  //字符串型
#define TEST4 'Q'    //字符型
#define TEST5 3.13159f   //float型
```
2. Effictive C++ 条款02
> 对于单纯常量，最好以const对象或enums替换#defines
> 对于形似函数的宏（macros）,最好改用inline函数替换#defines


3. 全局作用域声明的const变量可以被其他文件访问吗？
A:
```c++
const int aa=1;  //不可以被其他文件访问
extern const int bb=2;    //可以被其他文件访问
```

>  普通变量（非const）默认为extern。要使const变量能够在其他的文件中访问，必须显式地指定它为extern。


4. 其他重要点
- const对象必须要初始化，且不能修改
- 非const引用只能绑定到与该引用同类型对象。const引用则可以绑定到不同但相关的类型的对象或绑定到右值。
 **原因**  ——因为const只读，不会修改引用原对象
- 由于上面的原因，应该将不修改相应实参的形参定义为const引用。如果将这样的形参定义为非const引用，则毫无必要的限制了该函数的使用。

```c++
int const *x;   //指向const int值的指针x
const int *x;    //同上
int *const x = &initializedVar;    //指向int值的const指针，必须初始化
const int *const x = &initializedVar; //指向const int 值的const指针，必须初始化
```
- 如果使用引用形参的唯一目的是避免复制实参，则应将形参定义为const引用
- 函数的非引用形参，对于调用时的参数是否为const无影响
- const成员函数不能修改调用该函数的对象，const对象只能访问const成员函数,而非const对象可以访问任意的成员函数,包括const成员函数。所以对不修改成员变量的成员函数，应该定义为const成员函数。构造函数不能声明为const函数
5. Effictive C++ 条款03
```c++
vector<int> vec;
const iterator iter=vec.begin();    //int *const x
const_iterator cIter=vec.begin();   //int const* x
```


-----
### 1.1.8 static
#### 作用
> 修饰普通变量，修改变量的存储区域和生命周期，使变量存储在静态区，在main函数运行前就分配了空间，如果有初始值就用初始值初始化它，如果没有初始值系统用默认值初始化它。
> 修饰普通函数，表明函数的作用范围，仅在定义该函数的文件内才能使用。在多人开发项目时，为了防止与他人命令函数重名，可以将函数定位为static
> 修饰成员变量，修饰成员变量使所有的对象只保存一个该变量，而且不需要生成对象就可以访问该成员。
> 修饰成员函数，修饰成员函数使得不需要生成对象就可以访问该函数，但是在static函数内不能访问非静态成员。
1. 在data区存放已经初始化的全局静态变量，在bss区存放尚未初始化的全局静态变量，全部变量在main()外不需要写static。
2. 类中静态变量和外面的静态变量一样，由于声明为static的变量只被初始化一次，因为它们在单独的静态存储中分配了空间，因此类中的静态变量由对象共享。对于不同的对象，不能有相同静态变量的多个副本。也是因为这个原因，静态变量不能使用构造函数初始化。类中静态变量成员必须定义，因为声明的时候没有分配内存，定义可以给它分配内存，同时赋予了个初值；类中静态函数也同样不依赖于类对象，最好的调用方式为className::staticFucName();
```
class GamePlayer{
private:
	static const int NUM_Turns=5;   //声明的时候同时定义，因为静态常量变量只有一份拷贝，所以需要定义给它分配个内存
	int scores[NUM_TURNS];  //use of constant
	static print_hello(){cout<<"hello"<<endl;}
}
GamePlayer::print_hello();
}
```
3. 用static修饰的成员变量在对象中是不占内存的，因为他是不是跟对象一起在堆或栈中生成。
4. 加了static关键字的全局变量只能在本文件中使用。
5. 静态全局变量在该文件内可见，从变量定义处开始直到文件结束（普遍全局变量在同一程序的其他文件中可见，默认是external属性，而静态全局变量时internal属性），而在其他文件中不可见。

---
### 1.1.9 sizeof
必须知道的
> - 空类的大小为1字节
> - 一个类中，虚函数本身、成员函数（包括静态与非静态）和静态数据成员都是不占用类对象的存储空间
> - 对于包含虚函数的类，不管有多少个虚函数，只有一个虚指针，vptr的大小
> - 虚函数继承，不管是单继承还是多继承，都是继承了基类的vptr，几个基类，几个vptr的长度
> - 虚继承，继承基类的vptr


----

### 1.1.10 goto
避免使用goto控制流
在一些函数中，我们可能需要在return 语句之前做一些清理工作，比如释放在函数开始处有malloc申请的内存空间，使用goto总是一种简单的方法：
```c++
int f(){
	int *p = (int *)malloc(sizeof(int));
	*p = 10;
	cout<<*p<<endl;
#ifndef DEBUG
	int error=1;
#endif
	if(error)
		goto END;//do something when go wrong
END:
	cout<<"need free the *p"<<endl;
	free(p);
	return 0;
}
```
但由于goto不符合软件工程的结构化，而且可能使得代码难懂，所以不提倡使用，这个时候我们可以使用do{...}while(0)来做同样的事情
```c++
int ff(){
	int *p = (int*)malloc(sizeof(int));
	*p = 10;
	cout<<*p<<endl;
	do{
#ifndef DEBUG
	int error=1;
#endif
	//do something
	if(error)
		break;  //if error, out
	}while(0);
	cout<<"free"<<endl;
	free(p);
	return 0;
}
```


### 1.1.11 extern
C++与C编译区别
>在C++中常在头文件见到extern "C"修饰函数，那有什么作用呢？ 是用于C++链接在C语言模块中定义的函数。
>C++虽然兼容C，但C++文件中函数编译后生成的符号与C语言生成的不同。因为C++支持函数重载，C++函数编译后生成的符号带有函数参数类型的信息，而C则没有。
>例如int add(int a, int b)函数经过C++编译器生成.o文件后，add会变成形如add_int_int之类的, 而C的话则会是形如_add, 就是说：相同的函数，在C和C++中，编译后生成的符号不同。
>这就导致一个问题：如果C++中使用C语言实现的函数，在编译链接的时候，会出错，提示找不到对应的符号。此时extern "C"就起作用了：告诉链接器去寻找_add这类的C语言符号，而不是经过C++修饰的符号。

---
### 1.1.12 inline(内联函数)
1. 需要知道的
- 类中内联，类中定义的函数是隐式内联函数，如果只声明不定义则需要在定义处加inline成为内联函数
- 内联优势：提高函数执行效率，调用函数的工作：调用前要先保存寄存器，并在返回时恢复；复制实参；程序还必须指向一个新位置执行。
- 内联劣势：如果函数体内的代码较长，内联会导致内存消耗代价比较高。
- 内联函数应该在头文件中定义。把内联函数的定义放在头文件中，可以确保在调用函数时所使用的定义是相同的。


2. 虚函数可以是内联函数吗？
- 虚函数可以是内联函数，内联是可以修饰虚函数的，但是当虚函数表现多态性的时候不能内联。
- 内联是在编译器建议编译器内联，而虚函数的多态性在运行期，编译器无法知道运行期调用哪个代码，因此虚函数表现为多态性时（运行期）不可以内联。
- inline virtual 唯一可以内联的时候是：编译器知道所调用的对象是哪个类（如 Base::who()），这只有在编译器具有实际对象而不是对象的指针或引用时才会发生。

```c++
class Base
{
public:
    inline virtual void who()
    {
        cout << "I am Base\n";
    }
    virtual ~Base() {}
};
class Derived : public Base
{
public:
    inline void who()  // 不写inline时隐式内联
    {
        cout << "I am Derived\n";
    }
};

int main()
{
    // 此处的虚函数 who()，是通过类（Base）的具体对象（b）来调用的，编译期间就能确定了，所以它可以是内联的，但最终是否内联取决于编译器。 
    Base b;
    b.who();

    // 此处的虚函数是通过指针调用的，呈现多态性，需要在运行时期间才能确定，所以不能为内联。  
    Base *ptr = new Derived();
    ptr->who();

    // 因为Base有虚析构函数（virtual ~Base() {}），所以 delete 时，会先调用派生类（Derived）析构函数，再调用基类（Base）析构函数，防止内存泄漏。
    delete ptr;
    ptr = nullptr;

} 
```

---
### 1.1.13 union
> 联合（union）是一种节省空间的特殊的类，一个 union 可以有多个数据成员，但是在任意时刻只有一个数据成员可以有值。当某个成员被赋值后其他成员变为未定义状态。
> 默认访问控制符为public
> 可以含有构造函数、析构函数
> 不能含有引用类型的成员
> 不能继承自其它类，不能作为基类
> 不能含有虚函数
> 匿名union在定义所在作用域可直接访问union成员
> 匿名union不能包含protected成员或private成员
> 全局匿名联合必须是静态（static）的
> 
```c++
#include<iostream>

union UnionTest {
    UnionTest() : i(10) {};
    int i;
    double d;
};

static union {
    int i;
    double d;
};

int main() {
    UnionTest u;

    union {
        int i;
        double d;
    };

    std::cout << u.i << std::endl;  // 输出 UnionTest 联合的 10

    ::i = 20;
    std::cout << ::i << std::endl;  // 输出全局静态匿名联合的 20

    i = 30;
    std::cout << i << std::endl;    // 输出局部匿名联合的 30

    return 0;
}
```

---
### 1.1.14 四种cast转换
1. const_cast：用于将const变量转为非const
2. static_cast：用于各种隐式转换，比如非const转const，void\*转指针等，static_cast用于多态向上转化，如果能向下转能成功但是不安全，结果未知
3. dynamic_cast：用于动态类型转换。只能用于含有虚函数的类，用于类层次间的向上和向下转化。只能转指针或引用。向下转化时，如果是非法的对于指针返回NULL，对于引用抛异常。
4. reinterpret_cast：几乎什么都可以转，比如将int转指针，可能会出问题，尽量少用；
5. 为什么不用C的强制转换：C的强制转换表面上看起来功能强大什么都能转，但是转化不够明确，不能进行错误检查，容易出错。

---
### 1.1.15 智能指针
1. C++里面的四个智能指针：auto_ptr,shared_ptr,weak_ptr,unique_ptr其中后三个是C++11支持，并且第一个以及那个被11弃用。
2. 为什么要使用智能指针：智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题。因为智能指针就是一个类，当超出了类的作用域时，类会自动调用析构函数，析构函数会自动释放资源
。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。
3. unique_ptr实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象。它对于避免资源泄漏（例如”以new创建对象后因为异常退出忘记释放对象）特别有用
4. shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。资源被多个指针共享，它使用计数机制来表明资源被几个指针共享。可以通过成员函数use_count()来查看资源的所有者个数。除了可以通过new来构造，还可以通过传入另外三个智能指针来构造。当我们使用release()时，当前指针会释放资源所有权，计数减1。当计数等于0时，资源会被释放。

```c++
unique_ptr<string> p3(new string("auto"));
unique_ptr<string> p4;
p4 = p3;//error
```
---
### 1.1.16 字节对齐
```c++
#pragma pack(push)  // 保存对齐状态
#pragma pack(4)     // 设定为 4 字节对齐

struct test
{
    char m1;
    double m4;
    int m3;
};

#pragma pack(pop)   // 恢复对齐状态
```
---
### 1.1.17 位域
类可以将其（非静态）数据成员定义为位域（bit-field），在一个位域中含有一定数量的二进制位。当一个程序需要向其他程序或硬件设备传递二进制数据时，通常会用到位域。
- 位域在内存中的布局是与机器有关的
- 位域的类型必须是整形或枚举类型，带符号类型中的位域的行为将因具体实现而定
- 取地址运算符（&）不能作用域位域，任何指针都无法指向类的位域。


---
### 1.1.18 volatile
> volatile关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素（操作系统、硬件、其它线程等）更改。所以使用volatile告诉编译器不应对这样的对象进行优化
> volatile关键字声明的变量，每次访问时都必须从内存中取出值（没有被volatile修饰的变量，可能由于编译器的优化，从CPU寄存器中取值）
> const可以是volatile
> 指针可以是volatile


---
### 1.1.19 enum
[enum详细说明](https://blog.csdn.net/datase/article/details/82773937)
**how to use**

```c++
enum HUMAN{boy,girl,man,woman};
HUMAN sp=boy;//0
HUMAN jw=girl;//1
enum {boy,girl,man=4,woman};
int sp=boy;//0
int mama=woman;//5
typedf enum 
{
    boy,girl,man,woman,
}HUMAN;
```
---

### 1.1.20 decltype(c++11)
新特性，尚不了解杂用，先放上例子
```c++
// 尾置返回允许我们在参数列表之后声明返回类型
template <typename It>
auto fcn(It beg, It end) -> decltype(*beg)
{
    // 处理序列
    return *beg;    // 返回序列中一个元素的引用
}
// 为了使用模板参数成员，必须用 typename
template <typename It>
auto fcn2(It beg, It end) -> typename remove_reference<decltype(*beg)>::type
{
    // 处理序列
    return *beg;    // 返回序列中一个元素的拷贝
}
```

---
### 1.1.21 引用
**左值引用**
> 常规引用，一般表示对象的身份

**右值引用**
> c++11特性
> 右值引用就是必须绑定到右值（一个临时对象、将要销毁的对象）的引用，一般表示对象的值。
> 右值引用可实现转移语义（Move Sementics）和精确传递（Perfect Forwading），它的主要目的有两个方面
- 消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率
- 能够更简洁明确的定义泛型函数

**引用折叠**
- X& &、X& &&、X&& &可折叠成X&
- X&& &&可折叠成X&&


---

## 1.2 容器&泛型编程
### 1.2.2 顺序容器
1. **顺序容器**：将单一类型元素聚集起来成为容器，然后根据位置来存储和访问这些元素。（vector, list, deque）
    **适配器**：根据原始的容器类型所提供的操作，通过定义新的操作接口，来适应基础的容器类型。(stack, queue, priority_queue)
2. **默认构造函数**：所有容器类型都定义了默认构造函数，用于创建指定类型的空容器对象。默认构造函数不带参数。
3. **初始化**：
> 1将一个容器赋值给另一个容器时，类型必须匹配：容器类型和元素类型都必须相同
> 2用迭代器初始化，则不一定需要容器类型相同
```c++
vector<string> svec;
vector<string> svec2(svec);//ok  1
list<string> slist(svec);//err  1
list<string> slist2(svec);//ok  2
```
4. 容器元素类型必须满足以下两个约束。1元素类型必须支持赋值运算；2元素类型的对象必须可以复制。引用类型、标准IO和智能指针不能作为容器元素。
5. 关系操作符只适用于vector和deque，因为只有这两种容器为其元素提供快速、随机的访问
6. 在容器中添加元素时，系统是将元素值复制到容器里。被复制的原始值与新容器中的元素互不相关。因为容器在初始化的时候就分配了一定大小的内存空间，当添加元素时，只是把值复制进了容器中。
7. 不用将迭代器end赋值给临时对象，因为不管是添加还是删除操作，都会改变end指的值，而临时对象不会改变，即临时对象不是真正的end。
8. size指容器当前拥有的元素个数，而capacity指的是再下次分配内存前该容器可以存储的元素总数。
9. 所有适配器都定义了两个构造函数：默认构造函数用于创建空对象，而带一个容器参数的构造函数将参数容器的副本作为其基础值。默认的stack和queue都基于deque实现。

---
## 1.3 类
### 1.3.4 虚函数
1. 类的首地址是虚函数表地址，成员变量根据其继承和声明顺序依次放在后面，在单一继承中，被overwrite的虚函数在虚函数表中得到了更新
```c++
class Parent {
public:
    int iparent;
    Parent ():iparent (10) {}
    virtual void f() { cout << " Parent::f()" << endl; }
    virtual void g() { cout << " Parent::g()" << endl; }
    virtual void h() { cout << " Parent::h()" << endl; }

};

class Child : public Parent {
public:
    int ichild;
    Child():ichild(100) {}
    virtual void f() { cout << "Child::f()" << endl; }
    virtual void g_child() { cout << "Child::g_child()" << endl; }
    virtual void h_child() { cout << "Child::h_child()" << endl; }
};

class GrandChild : public Child{
public:
    int igrandchild;
    GrandChild():igrandchild(1000) {}
    virtual void f() { cout << "GrandChild::f()" << endl; }
    virtual void g_child() { cout << "GrandChild::g_child()" << endl; }
    virtual void h_grandchild() { cout << "GrandChild::h_grandchild()" << endl; }
};
```
```
 +-------+                            +-----------------------------------------------------+
0|       +----------------------->   0|GrandChild::f()                                      |
 | vfptr |                            +-----------------------------------------------------+
 +-------+                           1|Parent::g()                                          |
1|  10   |Parent.iparent              +-----------------------------------------------------+
 +-------+                           2|Parent::h()                                          |
2| 100   |Child.ichild                +-----------------------------------------------------+
 +-------+                           3|GrandChild::g_child()                                |
3|       |                            +-----------------------------------------------------+
 | 1000  |GrandChild.igrandchild     4|Child::h_child()                                     |
 +-------+                            +-----------------------------------------------------+
                                     5|GrandChild::h_grandchild()                           |
                                      +-----------------------------------------------------+
```
2. 多重继承（多个父类）：每个父类都有自己的续表，子类的成员函数被放到了第一个父类的表中，内存布局中，其父类布局一次按声明顺序排列。
3. [虚函数最详细解析](https://www.cnblogs.com/qg-whz/p/4909359.html#_label6_0)
4. 虚函数是动态绑定的，而缺省参数是静态绑定的
```c++
#include <iostream> 
using namespace std; 

class Base 
{ 
    public: 
        virtual void fun ( int x = 10 ) 
        { 
            cout << "Base::fun(), x = " << x << endl; 
        } 
}; 

class Derived : public Base 
{ 
    public: 
        virtual void fun ( int x=20 ) 
        { 
            cout << "Derived::fun(), x = " << x << endl; 
        } 
}; 


int main() 
{ 
    Derived d1; 
    Base *bp = &d1; 
    bp->fun();  // result:Derived::fun(), x = 10
    return 0; 
} 
```
5. 哪些函数不能为虚函数
- 普通函数
- 构造函数
- 静态成员函数
- 内联函数
- 友元函数
#### 1.3.4.1 虚继承
**问题**
菱形继承主要有数据冗余和二义性的问题。由于最底层的派生类继承了两个基类，同时这两个基类有继承的是一个基类，故而会造成最顶部基类的两次调用，会造成数据冗余及二义性问题。
**解决方案**
虚拟继承可以解决菱形继承的二义性和数据冗余的问题。
```c++
class Person
{
public :
    string _name ; // 姓名
};
class Student : virtual public Person
{
protected :
    int _num ; //学号
};
class Teacher : virtual public Person
{
protected :
    int _id ; // 职工编号
};
class Assistant : public Student, public Teacher
{
protected :
    string _majorCourse ; // 主修课程
};
void Test ()
{

    Assistant a ;
    a._name = "peter";
}
```
---
### 1.3.5 一个类至少有这4个函数
```c++
class Empty {
public:
	Empty(){ ... }    //default construct function
	Empty(const Empty& rhs){ ... }          //copy construct funtion
	~Empty(){ ... }                       //xi gou function
	Empty& operator=(const Empty& rhs){ ... } //copy assignment
};
Empty e1;   //call default construct function
Empty e2(e1);//call copy construct function
e2=e1;         //use copy assignment
```
---
### 1.3.6 构造函数
1. 构造函数可以被重载，实参决定使用哪个构造函数，构造函数自动执行，用于const对象的构造函数。
2. 构造函数多一个初始化列表，在初始化列表中对类成员进行初始化，而在构造函数体里只是对成员进行赋值。如果初始化列表里没有，会调用该类型的默认构造函数进行初始化，然后在函数体里被重新赋值。
3. 初始化const或引用类型数据成员的唯一机会在构造函数初始化列表中。
```c++
class ConstRef {
public:
	ConstRef(int ii);
private:
	int i;
	const int ci;  //只能在初始化时进行赋值
	int &ri;  //只能在初始化时进行赋值
};
ConstRef::ConstRef(int ii):i(ii),ci(i),ri(ii){}
```

4. 构造函数初始化列表仅指定用于初始化成员的值，并不指定这些初始化执行的次序。成员被初始化的次序就是定义成员的次序。
5. explicit 修饰构造函数时，可以防止隐式转换和复制初始化
ex:
```c++
class Test1
{
public:
    Test1(int n)            // 普通构造函数
    {
        num=n;
    }
private:
    int num;
};

class Test2
{
public:
    explicit Test2(int n)   // explicit（显式）构造函数
    {
        num=n;
    }
private:
    int num;
};

int main()
{
    Test1 t1=12;            // 隐式调用其构造函数，成功
    Test2 t2=12;            // 编译错误，不能隐式调用其构造函数
    Test2 t2(12);           // 显式调用成功
    return 0;
}
```
6. 构造函数要做到线程安全，唯一的要求是在构造期间不要泄露this指针。（比如，在构造函数内不要注册回调函数，因为可能还没构造结束，最后一行也不行，因为可能是基类，自身构造函数构造完，会构造子类），解决方案是构造函数+Initialize函数。

---
### 1.3.7 析构函数
> 基类的析构函数必须为虚函数，在实现多态时，当用基类操作派生类，在析构时防止只析构基类而不析构派生类的状况发生。

---
### 1.3.8 友元
> 友元提供了一种 普通函数或者类成员函数 访问另一个类中的私有或保护成员 的机制， 能访问私有成员 - 破坏封装性 - 友元关系不可传递 - 友元关系的单向性 - 友元声明的形式及数量不受限制
---
### 1.3.9 类模板和模板类（无用的冷知识）
**类模板**
```c++
template<typename T>
class A{}
```
**模板类**

```c++
A<int>
```

---
## 1.5编译与底层
### 1.5.1 编译机制
1. 对于C++源文件，从文本到可执行文件一般需要四个过程：
> 预处理阶段：对源代码文件中文件包含关系（头文件）、预编译语句（宏定义）进行分析和替换，生成预编译文件。
> 编译阶段：将经过预处理后的预编译文件转换城特定汇编代码，生成汇编文件
> 汇编阶段：将编译阶段生成的汇编文件转化成机器码，生成可重定位目标文件
> 链接阶段：将多个目标文件及所需要的库链接成最终的可执行目标文件
2. include头文件的顺序：对于include 的头文件来说，如果在a.h中声明一个在b.h中定义的变量，而没有引用b.h。那么要在a.c文件中引用b.h，并且要先引用b.h，后引用a.h，否则会报变量类型未声明错误。
3. 使用" "，搜索头文件的顺序为：当前头文件目录-->编译器设置的头文件路径-->系统变量CPLUS_INCLUDE_PATH/C_INCLUDE_PATH指定的头文件路径
4. 使用<>，搜索头文件的顺序为：编译器设置的头文件路径-->系统变量CPLUS_INCLUDE_PATH/C_INCLUDE_PATH指定的头文件路径


---
### 1.5.2 内存分配和管理
**malloc calloc realloc alloca**
> malloc：申请指定字节数的内存。申请到的内存中的初始值不确定
> calloc：为指定长度的对象，分配能容纳其指定个数的内存。申请到的内存的每一位（bit）都初始化为0。
> realloc：更改以前分配的内存长度（增加或减少）。当增加长度时，可能需将以前分配区的内容移到另一个足够大的区域，而新增区域内的初始值则不确定
> alloca：在栈上申请内存。程序在出栈的时候，会自动释放内存。但需要注意的是，alloca不具可移植性，而且在没有传统堆栈的机器上很难实现。
> 
```c++
char *str = (char*) malloc(100);
free str;
str = nullptr;
```


**new/delete**

1. new/new[]:先malloc后调用构造函数
2. delete/delete[]:先调用析构函数，后free
3. new在申请内存时会自动计算所需字节数，而malloc则需我们自己输入申请内存空间的字节数

## 1.6 STL
### 1.6.1 总结
> 容器：隐藏了内存问题，不需要管内存 
> 分配器：解决内存问题，支持容器的内存处理 
> 算法：容器的操作 
> 迭代器：容器和算法的桥梁，泛化的指针 
> 仿函数（functors） 适配器
---

# 2 计算机网络
## 2.1 网络概述
1. 五层网络协议
```c++


+----------------------+
|                      |
|      应用层           |  特殊进程之间特定的通信协议。（FTP/IMAP）
+----------------------+
|                      |
|      传输层           |  为计算机中不同进程之间的通信存在的协议。（TCP/UDP）
+----------------------+
|                      |
|      网络层           |  为识别不同网口（比如WIFI和以太网）的通信存在的协议。（IP）
+----------------------+
|                      |
|      连接层           |  信息以帧为单位传输。连接层协议的功能就是识别0/1序列中包含的帧。（以太网/WIFI）
+----------------------+
|                      |
|      物理层           |  通过将信号转换为1和0在媒介进行传输
+----------------------+

```

---

## 2.2 连接层
1. 以太网和WiFi是连接层的两种协议。信息以帧为单位传输。帧结构包括头部（序言、起始信号、目的地、发出地和类型）、数据和尾部（校验序列）

```c++
    content: Preamble |SFD|Destination|Source|Type|payload（data）|fcs|extension  
    bit :    ---7---- |-8-|6----------|6-----|16----------------  |   |
```
2. mac地址是物理设备自带的序号，只能在同一个以太网中被识别（正如邮差只熟悉自己的社区一样）

---
## 2.3 网络层
1. IP包格式
```
    |-------------------------------------------------------- --- ------|  
    |-version-|-ihl--|-type of service-|--------total-length------------|  
    |------identificaiton--------------|-flags-|-fragment offset------- |  
    |-time-to-live---| protocol        | header checksum----------------|  
    |--------------------source address--------------------------- -----|  
    |-----------------------destination address------------------- -----|  
    |---------options--------------------- --|---- -----padding---------|  
    |-----------------------------data--------------------------------- |
```
2. IP协议像是接力棒，通过路由表判断将信息送到哪个网卡。
3. ARP协议：找到IP地址和MAC的对应关系
4. RIP协议：智能拓展routing table
5. 
---
## 2.4传输层协议
###  2.4.1 TCP [学习源地址](https://coolshell.cn/articles/11564.html) [学习源地址2](https://www.cnblogs.com/linguoguo/p/10519635.html)
1. tcp包
> sequence number是包的序号，用来解决网络包乱序(reordering)问题。 
> Acknowledgement Number就是ACK——用于确认收到，用来解决不丢包的问题。
> Window又叫Advertised-Window，也就是著名的滑动窗口(Sliding Window)，用于解决流控的。
> TCP Flag也就是包的类型，主要是用于操控TCP的状态机的。
2. TCP的状态机
> 网络上的传输是没有连接的，包括TCP也是一样的。而TCP所谓的“连接”，其实只不过是在通讯的双方维护一个“连接状态”，让它看上去好像有连接一样。
```c++
                          +--------------+
                          |              |
       +------------------+   CLOSED     +--------------+
       |                  +--------------+              |
       |                                                |
       |                                                |
       |Passive Open                                    |
       |Set Up TCB                                      |
       |                                                |
       |                                                |
       v                                     Active Open Set Up TCB
+------+-------+                                  Send SYN
|              |        <-------------------------------| connect()
|   LISTEN     |listen()        SYN  seq=x              |
+-----+--------+                                        |
      |                                                 |
      |                                                 |
      | Receive SYN    +---------------------------->   |
      | Send SYN+ACK       SYN seq=y ACK=x+1            |
      |                                                 |
      |                                                 v
+-----v---------+       Simultaneous Open         +-----+-------+
|               |     Receive SYN Send ACK        |             |
| SYN-RECEIVED  +<--------------------------------+ SYN-SENT    |
+-------+-------+                                 +-----+-------+
        |             <------------------------+        |
        |                  ACK=y+1                      |
        |                                               |
        |Receive ACK                                    |Receive SYN+ACK
        |                                               |Send ACK
        |                                               |
        |              +-------------------+            |
        |              |                   |            |
        +------------->+   ESTABLISHED     +<-----------+
                       +-------------------+
                         |      |
      FIN seq=x+2 ACK=y+1|      |         Recei^e FIN Send ACK=x+3
           +-------------+      +----------------+
           |   close()                           |
    +------v-------+                      +------v-------+
    |  FIN-WAIT-1  |                      |  CLOSE-WAIT  |
    +---+------+---+                      +--------------+
        |      |                                  |
        |      |                                  |
        |      |                                  |
        |      |  Recieve Fin Send ACK            |
        |      +------------+                     |Wait for Application Close
        |                   |                     |  Send FIN
Receive ACK for FIN         |                     |
        ^                   v                     v
   +----+--------+      +---+---------+   +-------+----+
   |  FIN-WAIT-2 |      |  CLOSING    |   |  LAST-ACK  |
   +-------------+      +---+---------+   +---------+--+
 Receive FIN                |                       |
    Send ACK                |Receive ACK for FIN    |
        |     +-------------v+                      v
        +-----> TIME-WAIT    |                    +-+-------------+
              |              +------------------->+               |
              +--------------+                    |    CLOSED     |
                                                  +---------------+


```
3. 当server端socket在SYN-RECEIVED状态时，client端断链了，会重试发SYN-ACK，时间间隔为1s,2s,4s,8s,16s，总共31s，第5次发出后等待32s，如果没有回复就会断开这个链接。
> **关于SYN FLOOD攻击**：给服务器发一个SYN就下线，于是服务器需要默认等待63s才会断开连接，这样就可以把服务器的syn连接的队列耗尽，让正常的连接请求不能处理。
> **解决方案**：Linux下给了一个叫tcp_syncookies的参数来应对这个事——当SYN队列满了之后，TCP会通过源地址端口、目标地址端口和时间戳打造出一个特别的Sequence Number发回去（又叫cookie），如果是攻击者则不会有响应，如果是正常连接，则会把这个SYN Cookie发回来，然后服务端可以通过cookie建连接（即使不在SYN队列中）；tcp_synack_retries参数减少重试次数；tcp_max_syn_backlog参数增大SYN连接数；tcp_abort_on_overflow参数实在处理不过直接拒绝连接
4. **关于ISN的初始化**：ISN不能hard code，会在网络不稳定时，接收端无法确定包的顺序和是否有用。RFC793里规定，ISN会和一个假的时钟绑在一起，这个时钟会在每4微妙对ISN做加1的操作，直到超过2^32，又从0开始。这样一个ISN的周期大约是4.55小时，因为我们假设我们的TCP Segment在网络上的存活时间不会超过Maximum Segment Lifetime，所以只要MSL的值小于4.55小时，那么我们就不会重用到ISN。
5. **关于MSL和TIME_WAIT**：最后发出ACK，肯定要想一下对面有没有收到啊，因为TCP Segment最迟会花费1个MSL送到对面，如果对面1个MSL都没收到你的ACK，对面就会再次发一个FIN给你，就又得等1个MSL。一来一回，两个MSL，所以在发出ACK后，会进入TIME_WAIT状态，等待2个MSL时间，socket状态才会CLOSED。
6. **超时重传机制** ：在未收到该收到的包后，就不发ACK，知道发送端发觉TIME OUT后，重传缺失的包。**问题**：等待timeout 时间太久，也不知道要重传哪些包，可能会浪费带宽
7. **快速重传机制**：Fast Retransmit，接收端在未收到该收到的包后，会重复发上一个接收的包的ACK，连发三次后，发送端知道这个包没收到，会重发。**问题**解决了不需要等待timeout，但还是没解决不知道要重传哪些包的问题。
8. **SACK方法**：这种方式在TCP头里加一个SACK的东西，ACK还是Fast Retransmit的ACK，SACK则是汇报接收到的数据碎版。
9. **TCP的RTT算法**：RTT是一个数据包从发出去到回来的时间。RTO（Retransmission Time Out）超时时间
> **经典算法** ：每次采样RTT值，便与SRTT加权求出新的SRTT。缺陷：很难处理重传时候的RTT值。
> **Karn/Partrigdge**算法：忽略重传，不把重传的RTT做采样，如果遇到重传直接double rto值。缺陷：这种死规矩对于一个需要估计比较准确的RTT不靠谱。
> **Jacobson/Karels**:消除当RTT有大波动的情况
10. **TCP滑动窗口**：TCP头里有一个字段叫Window，又叫Advertised-Window，这个字段是接收端告诉发送端自己还有多少缓冲区可以接收数据。于是发送端就可以根据这个接收端的处理能力来发送数据，而不会导致接收端处理不过来。
11. **TCP的拥塞处理**：如果网络上的延时突然增加，那么，TCP对这个事做出的应对只有重传数据，但是，重传会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，于是，这个情况就会进入恶性循环被不断的放大。
> **慢启动**：window一开始很小，然后每成功传输一次，窗口大小加1，每过一个RTT时间，窗口大小double。说是慢启动，但当网络情况好的时候，速度很快就上来了，因为网络情况好，收到ACK的时间快，收到ACK的时间短，所以RTT时间就短，window大小就一直double。
> **拥塞避免算法**：当window到一个很大的值后（ssthresh=65535字节），会慢慢增长，不再会直接double。
> **拥塞状态算法**：当重传发生时，会重置window窗口大小为1，然后重新开始慢启动，不同的是，ssthresh会减半。
> **快速恢复算法**：一般与快速重传算法一起使用，当收到3个Duplicated ACK后，说明网络也没那么糟糕，调整窗口为（sshthresh+3*MSS）。
> **FACK算法**：用来做SACK重传过程中的拥塞流控。
12. **SYN队列**：SYN队列存储了收到SYN包的连接，它的职责是回复SYN+ACK包，并且在没有收到ACK包时重传，直到超时。发送完SYN+ACK后，SYN队列等待从客户端发出的ACK包。当收到ACK包时，首先找到对应的SYN队列，再在对应的SYN队列中检查相关的数据看是否匹配。内核将该连接相关的数据从SYN队列中移除，创建一个完整的连接，并将这个连接加入到Accept队列。
13. **Accept队列**：Accept队列中存放的是已建立好的连接，也即等待被上层应用程序取走的连接。当进程调用accpet()，这个socket从队列中取出，传递给上层应用程序。
14. **队列大小设置多少合适**：看情况，对于大多数的TCP服务来说，这并不重要。尽管如此也存在一些合理的原因，需要增大队列的大小
> 当建立连接的请求速度缺失很大时，即使是对于一个高性能的服务来说，SYN队列也可能需要设置的大一些。
> SYN队列的大小，换言之就是等待ACK包的连接数。也即与客户端的平均往返时间越大，堆积在SYN队列中的连接就越多。对于那些大部分客户端都距离服务器很远的场景。比如说往返时间几百毫秒以上，可以将队列大小设置的大一些。
> TCP_DEFER_ACCPET选项如果打开了，会导致socket在SYN-RECV状态下维持更长的时间，也即增大了处于SYN队列中的时间。
> 队列设置太大的缺点：syn队列中的每一个槽位都需要占用一些内存。当遇到SYN_Flood攻击时，我们没必要为这些发起攻击的包浪费资源。SYN队列中的inet_request_sock结构体，在4.14内核下，每个将占用256字节的内存。
15. **查看socket状态命令**：
```bash
ss -n state syn-recv sport=:80|wc -l    
#查看80端口处于syn-recv状态的socket数量
```
16.  **循环抓包法**
命令：
```bash
nohup tcpdump -i eth3 host 192.168.201.132 and port 63701 -s0 -C100 -W100 -w /root/ts_dump.pcap -Z root > /dev/null &
```
> 命令说明：  
> -i eth3 其中eth3----替换为IP所在的网口名称
> host 192.168.201.132 其中IP替换为频道收流的IP地址
> port 63701 其中63701替换为频道收流的端口
> -C100 文件大小100MB
> -W100 文件滚动个数100个
> -w /root/ts_dump.pcap 文件存放路径
> 预估可以抓包保存的时间：100MB（约4分钟），100个文件约（400分钟，大概在6-7小时），可以根据系统盘剩余空间适当调整下文件个数

中断抓包命令：
```bash
pkill -9 tcpdump
```
---

# 3 操作系统
## 3.1操作系统概论
### 3.1.1虚拟内存
1. 虚拟内存是操作系统物理内存和进程之间的中间层，它为进程隐藏了物理内存这一概念，为进程提供了更加简洁和易用的接口以及更加复杂的功能。
2. 操作系统以页为单位管理内存，当进程发现需要访问的数据不在内存时，操作系统可能会将数据以页的方式加载到内存中，这个过程是由MMU完成的。
3. 虚拟内存的关键作用
> 虚拟内存可以利用磁盘起到缓存的作用，提高进程访问磁盘的速度；
> 虚拟内存可以为进程提供独立的内存空间，简化程序的链接、加载过程并通过动态库共享内存
> 虚拟内存可以控制进程对物理内存的访问，隔离不同进程的访问权限，提供系统的安全性。
4. 当用户程序访问未被缓存的虚拟页时（即没有被缓存到物理内存中的数据），硬件就会触发缺页中断（Page Fault,PF），一种情况：被访问的页面已经加载到了物理内存中，但用户程序的页表（Page Table）并不存在该对应关系，这时我们只需要在页表中建立虚拟内存到物理内存的关系；另一种情况：操作系统需要将磁盘上未被缓存的虚拟页加载到物理内存中。
5. 在Linux调用fork创建子进程时，实际上只复制了父进程的页表，当父进程或者子进程对共享的内存进行修改时，共享的内存才会以页为单位进行拷贝，父进程会保留原有的物理空间，而子进程会使用拷贝后的物理空间。
### 3.1.2 中断
1. 
> 中断又分为：
> 硬中断：外围硬件比如网卡发给CPU的信号
> 软中断：由硬中断处理后对操作系统内核发出信号的中断请求
2. 网卡中断
```bash
cat /proc/interrupts
           CPU0       CPU1
  0:        138          0   IO-APIC-edge      timer
  1:          1          2   IO-APIC-edge      i8042
  8:          1          0   IO-APIC-edge      rtc0
  9:          0          0   IO-APIC-fasteoi   acpi
 12:          3          1   IO-APIC-edge      i8042
 16:          0          0   IO-APIC-fasteoi   uhci_hcd:usb3
 17:          1          0   IO-APIC-fasteoi   radeon
 18:          0          0   IO-APIC-fasteoi   ehci_hcd:usb1, uhci_hcd:usb8, i801_smbus
 19:          0          0   IO-APIC-fasteoi   uhci_hcd:usb5, uhci_hcd:usb7
 21:          0          0   IO-APIC-fasteoi   uhci_hcd:usb4
 23:          0          0   IO-APIC-fasteoi   ehci_hcd:usb2, uhci_hcd:usb6
 24:          0          0   PCI-MSI-edge      aerdrv, PCIe PME
 25:          0          0   PCI-MSI-edge      aerdrv, PCIe PME
 26:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 27:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 28:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 29:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 30:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 31:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 32:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 33:     161714     162023   PCI-MSI-edge      eth0
 35:      53554      53247   PCI-MSI-edge      0000:00:1f.2
 36:          1          0   PCI-MSI-edge      ioat-msi

```
第一列为中断号，查看该中断绑定的CPU中断亲和性。03表示该网卡中断会轮流指定到CPU0和CPU1
```bash
cat /proc/irq/33/smp_affinity
03
```
网卡在接收到ipv4 tcp的数据流时，根据指定的字段计算hash值（例如Toeplitz哈希算法），之后将hash值的后7位数值作为索引，到网卡的对照表中找到相应的队列索引，将数据包添加到此队列中。
【**现场故障**】：网卡驱动只对组播目的端口进行了一次Hash，现网情况下所有组播频道使用相同的目的端口，所有报文需要遍历链表进行查找处理，十分消耗CPU。**改进措施**：进行二次hash，除了使用组播目的端口进行第一次hash外，还对组播目的地址进行第二次hash，查找速度就很快。

### 3.1.3 性能（命令）
1.查看性能的命令
```bash
#1. uptime
#快速展示系统平均负载的方法，这也指出了等待运行进程的数量
#三个数字分别统计过去1、5、15分钟的平均数
[root@localhost ZMSS]# uptime
 15:07:50 up  3:52,  1 user,  load average: 1.51, 1.60, 1.57
#2. dmesg | tail
#这里展示的是最近10条系统消息日志，主要是看由于性能问题导致的错误
[root@localhost ZMSS]# dmesg | tail
[  197.648482] 111111entry tipc_send,msg_sect->iov_len:0
[  197.772422] 111111entry tipc_send,msg_sect->iov_len:0
[  197.897982] 111111entry tipc_send,msg_sect->iov_len:0
[  198.079991] 111111entry tipc_send,msg_sect->iov_len:0
[  198.190386] 111111entry tipc_send,msg_sect->iov_len:0
[  209.443797] perf: interrupt took too long (3145 > 3137), lowering kernel.perf_event_max_sample_rate to 63000
[  211.109880] 111111entry tipc_send,msg_sect->iov_len:28
[  280.512999] perf: interrupt took too long (3943 > 3931), lowering kernel.perf_event_max_sample_rate to 50000
[  434.259920] perf: interrupt took too long (4930 > 4928), lowering kernel.perf_event_max_sample_rate to 40000
[ 5779.905683] hrtimer: interrupt took 5914 ns

#3.vemstat 1
#对虚拟内存统计的简短展示
#r:cpu上的等待运行的可运行进程数，这个指标提供了判断CPU饱和度的数据，因为它不包含IO等待的进程
#free:空闲内存，单位是k。
#si,so：交换进来和交换出去的数据量，如果这两个值非0，那么就说明没有内存了（用到swap了）
#us,sy,id,wa,st:这些是CPU时间的分解，是所有CPU的平均值，分别为用户时间，系统时间，空闲，等待IO时间和被偷的时间
#CPU时间分解可以帮助确定CPU是不是非常忙（通过用户时间和系统时间累加判断）持续的IO等待则表明磁盘是瓶颈
[root@localhost ZMSS]# vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 2196724 131440 4463008    0    0    15   124  959 1001  6 56 36  2  0
 1  0      0 2195960 131444 4463016    0    0     0    28 11144 14342  6 52 41  2  0
#4.mpstat -P ALL 1
#打印各个CPU的时间统计，可以看出整体CPU的使用是不是均衡的。

[root@localhost ZMSS]# mpstat -P ALL 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

15:10:29     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
15:10:30     all    6.03    0.00   50.25    1.01    1.01    0.00    0.00    0.00    0.00   41.71
15:10:30       0    5.10    0.00   45.92    3.06    1.02    0.00    0.00    0.00    0.00   44.90
15:10:30       1    6.86    0.00   53.92    0.00    0.98    0.00    0.00    0.00    0.00   38.24
#5.pidstat 1
#为每个CPU统计信息功能
[root@localhost ZMSS]# pidstat 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

15:12:04      UID       PID    %usr %system  %guest    %CPU   CPU  Command
15:12:05        0      6061    0.00    0.87    0.00    0.87     1  kill_abnormal_s
15:12:05        0      6468    0.00    3.48    0.00    3.48     0  ZMSSdu
15:12:05        0      6777    0.00    0.87    0.00    0.87     0  ZMSSRtspOverUDP
15:12:05        0      6778    0.00    0.87    0.00    0.87     1  ZMSSStreamingSe
15:12:05        0     10755    0.87    6.09    0.00    6.96     0  pidstat
15:12:05        0     10824    0.00    7.83    0.00    7.83     1  ps
15:12:05        0     11341    0.00    0.87    0.00    0.87     1  ZMSSCC
15:12:05        0     13626    0.00    0.87    0.00    0.87     0  ZXUSS_CGSL_CDNL
15:12:05        0     13778    0.00    0.87    0.00    0.87     0  ZXUSS_CGSL_USSP
15:12:05        0     13829    0.87    0.00    0.00    0.87     1  ZXUSS_CGSL_CDNC
#6.iostat -xz 1
#r/s,w/s,rkB/s,wkB/s:表示设备上每秒钟的读写次数和读写的字节数，这些可以看出设备的负载情况
#await:I/O等待的平均时间这是应用程序所等待的时间，包含了等待队列中的时间和被调度服务的时间。
[root@localhost ~]# iostat -xz 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           6.90    0.00   56.21    1.76    0.00   35.13

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sda               0.25    32.59    1.29    5.82    30.23   243.52    77.00     0.29   40.56  102.52   26.77   8.05   5.72

#7.free -m
#buffers:用于块设备I/O缓冲的缓存
#cached:用于文件系统的页缓存
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           7470         848        1656          87        4966        5279
Swap:             0           0           0
#8.sar -n DEV 1
#使用这个工具是可以检测网络接口的吞吐：rxkB/s和txkB/s。作为收发数据负载的度量，也是检测是否到达收发极限
#如下例，接收数据2.46kB/s
[root@localhost ~]# sar -n DEV 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)
16:03:16        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
16:03:17         eth0     24.00      5.00      2.46      0.54      0.00      0.00      0.00

#9.sar -n TCP,ETCP 1
#这是对TCP关键指标的统计
#active/s:每秒本地发起的TCP连接数（例如通过connect()发起的链接）
#passive/s：每秒远程发起的连接数（例如通过accept()接收的链接）
#retran/s：每秒TCP重传数
#这种主动和被动统计数通常用作对系统负载的粗略估计
[root@localhost ~]# sar -n TCP,ETCP 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

16:08:18     active/s passive/s    iseg/s    oseg/s
16:08:19         2.00      0.00      9.00     19.00

16:08:18     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
16:08:19         0.00      0.00      0.00      0.00      0.00
```
---
### 3.1.4  链接
1. 处理目标文件的工具

| 命令    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| AR      | 创建静态库，插入、删除、列出和提取成员                       |
| strings | 列出一个目标文件中所有可打印的字符串                         |
| strip   | 从目标文件中删除符号表信息                                   |
| nm      | 列出一个目标文件的符号表中定义的符号                         |
| size    | 列出目标文件中节的名字和大小                                 |
| readelf | 显示一个目标文件的完整结构，包括elf头中编码的所有信息。包含size和nm的功能 |
| objdump | 所有二进制公安局之母。能够显示一个目标文件中所有的信息。它最大的作用是反汇编.text节中的二进制指令 |
| ldd     | 列出一个可执行文件在运行时所需要的共享库                     |

2.  链接器必须完成两个主要任务：
- 符号解析：目标文件定义和引用符号，每个符号对应于一个函数、一个全局变量或一个静态变量（即C语言中任何以static属性声明的变量）。符号解析的目的是将每个符号引用正好和一个符号定义**关联**起来。
- 重定位：编译器和汇编器生成从地址0开始的代码和数据节。链接器通过把每个符号定义与一个内存位置关联起来，从而重定位这些节。
3.  目标文件——三种形式（四种）
- 可重定位目标文件——编译器和汇编器生成的目标文件 (.o)
- 可执行目标文件——链接器生成的目标文件(.exe)
-  共享目标文件——一种特殊类型的可重定位目标文件，可以在加载或者运行时被动态地加载进内存并链接。(.so)
- 核心转储文件(Core Dump File)——当进程意外终止时，系统可以将该进程的地址空间的内容及终止时的一些其他信息转储到核心转储文件（Linux下的core dump）
4. 典型的ELF可重定位目标文件
```
+-----------------+
|     ELF head    |
|     .text       |
|     .rodata     |
|     .data       |
|     .bss        |
|     .symtab     |
|     .rel.text   |
|     .rel.data   |
|     .debug      |
|     .line       |
|     .strlab     |
|    节 头 部 表   |
+-----------------+

```
|   名称    | 解释                                                         |
| :-------: | :----------------------------------------------------------- |
|   .text   | 已经编译程序的机器代码                                       |
|  .rodata  | 只读数据，比如printf语句中的格式串和开关语句的跳转表         |
|   .data   | 已初始化的全局和静态C变量。局部C变量在运行时保存在栈中，既不出现在.data节中，也不出现在.bss节中。 |
|   .bss    | 未初始化的全局和静态C变量，以及所有被初始化为0的全局或静态变量。 |
|  .symtab  | 一个符号表，它存放在程序中定义和引用的函数和全局变量的信息。 |
| .rel.text | 一个.text节中位置的列表，当链接器把这个目标文件和其他文件组合时，需要修改这些位置。 |
| .rel.data | 被模块引用或定义的所有全局变量的重定位信息。                 |
|  .debug   | 一个调试符号表。以-g选项调用编译器才会有                     |
|   .line   | 原始C源程序中的行号和.text节中机器指令之间的映射。以-g选项调用编译器才会有 |
|  .strtab  | 一个字符串表，其内容包括.symtab和.debug节中的符号表          |
---
### 3.1.5 线程
- 一个标准的线程由线程ID、当前指令指针（PC）、寄存器集合和堆栈组成
- 各个线程之间共享程序的内存空间（包括代码段、数据段、堆等）及一些进程级的资源（如打开文件和信号）。
- Linux将所有的执行实体（无论线程还是进程）都称为任务（TASK），每一个任务概念上都类似于一个单线程的进程，具有内存空间、执行实体、文件资源等。不同的任务之间可以选择共享内存空间。因而在实际 意义上，共享了同一个内存空间的多个任务构成了一个进程，这些任务也就成了这个进程里的线程。
---
## 3.2文件
### 3.2.1 文件描述符
- 遇到个文件描述符满的问题，```bash \lsof -p pid```，可以查看某一进程的fd使用数量。```lsof -n|awk '{print $2}'|sort|uniq -c|sort -nr|more```查看哪个进程用的fd最多
- 如何排除是文件描述符泄露问题，下线所有用户，看fd数量是否还在
- Type 列则比较直观。文件和目录分别称为 REG 和 DIR。而CHR 和 BLK，分别表示字符和块设备；  
或者 UNIX、FIFO 和 IPv4，分别表示 UNIX 域套接字、先进先出 (FIFO) 队列和网际协议 (IP) 套接字。
---
## 3.3 实操
### 3.3.1 模拟单核CPU冲高
```bash
cat /dev/urandom | gzip -9 > /dev/null
#然后进行绑核
taskset -cp 0 pid 
#此处0表示cpu0
```
- top命令，进去后按1，可以看单个cpu使用情况


---

## 3.4 进程与线程
### 3.4.1 相关函数调用

| 功能     | 进程                                   | 线程                                   |
| -------- | -------------------------------------- | -------------------------------------- |
| 创建     | fork()                                 | pthread_create()                       |
| 退出     | exit()                                 | pthread_exit()                         |
| 等待     | wait/waitpid()                         | pthread_join()                         |
| 取消     | abort()                                | pthread_cancel()                       |
| 获取ID   | getpid()                               | pthread_self()                         |
| 调度策略 | SCHED_OTHER、SCHED_FIFO、SCHED_RR      | SCHED_OTHER、SCHED_FIFO、SCHED_RR      |
| 通信机制 | 管道、消息队列、共享内存、信号、信号量 | 信号、信号量、互斥锁、读写锁、条件变量 |



# 5 设计模式
## 5.1 设计原则
1. 依赖倒置原则（DIP）
- 高层模块（稳定）不应该依赖于低层模块（变化），二者都应该依赖于抽象（稳定）
- 抽象（稳定）不应该依赖于实现细节（变化），实现细节应该依赖于抽象（稳定）

2. 开放封闭原则（CCP）
- 对扩展开放，对更改封闭
- 类模块应是可扩展，但是不可修改

3. 单一职责原则（SRP)
- 一个类应该仅有一个引起它变化的原因
- 变化的方向隐含着类的责任

4. Liskov替换原则（LSP)
- 子类必须能够替换它们的基类（IS-A）
- 继承表达类型抽象

5. 接口隔离原则（ISP）
- 不应该强迫客户程序依赖它们不用的方法
- 接口应该小而完备

6. 优先使用对象组合，而不是类继承
- 类继承通常为“白箱复用”，对象组合通常为“黑箱复用”
- 继承在某种程度上破坏了封装性，子类父类耦合度高
- 而对象组合则只要求被组合的对象具有良好定义的接口，耦合度低。

7. 封装变化点
- 使用封装来创建对象之间的分界层，让设计者可以在分界层的一侧进行修改，而不会对另一侧产生不良的影响，从而实现层次间的耦合

8. 针对接口编程，而不是针对实现编程
- 不将变量类型声明为某个特定的具体类，而是声明为接口
- 客户程序无需获知对象的具体类型，只需要知道对象所具有的接口
- 减少系统中各部分的依赖关系，从而实现“高内聚、松耦合”的类型设计方案


## 5.2 单例模式
1. 懒汉和饿汉
```c++
class lazySingleton {
private:
	lazySingleton(){}
	static lazySingleton *p;
public:
	static lazySingleton *instance();
};
lazySingleton *(lazySingleton::p) = nullptr;
lazySingleton* lazySingleton::instance(){
	if(p == nullptr){
		p = new lazySingleton();
	}
	return p;
}

class hungrySingleton(){
private:
	hungrySingleton(){}
	static hungrySingleton *p;
public:
	static hungrySingleton* instance();
}
hungrySingleton *(hungrySingleton::p) = new hungrySingleton();
hungrySingleton* hungrySingleton::instance(){
	return p;
}
```
2. 很明显饿汉是线程安全的，这里的线程安全其实是针对instance而不是类成员p，懒汉则因为多线程判断(p==nullptr)，可能会生成多个实例，所以需要加锁判断
。但是如果直接在if前加锁，会在每次调用时都会判断锁，而其实该函数只要被调用一次后，就不会再出现线程不安全的问题。所以采用双重检查锁模式，在上述锁前再加一层if(p\==nullptr)的判断。
```c++
lazySingleton* lazySingleton::instance(){
	if(p == nullptr){
		Lock lock;
		if(p == nullptr){
			p = new lazySingleton();
		}
	}
	return p;
}
```
3. 双重检查锁模式仍有个问题，是内存读写的乱序执行，即p可能还未构造完成，就被别的线程返回了。
4. 更简洁的一种单例模式写法，用类中的静态成员变量实现
```c++
singleton* singleton::instance(){
	static singleton p;
	return &p;
}
```

---
# 6 数据结构与算法
 ## 6.1跳表
 1. 跳表和红黑树很像，都可以支持快速的插入、删除、查找操作。
 2. 如何避免复杂度退化，以及查找、插入、删除操作性能下降？
 A:跳表是通过随机函数来维护平衡性，通过一个随机函数，来决定这个结点插入到哪几级索引中。比如随机函数生成了值K，那我们将这个节点添加到第一级到第K级这K级索引中。
 3. Redis为什么要用跳表来实现有序集合，而不是红黑树？
 A:Redis的有序集合是通过跳表来实现的，Redis中的有序集合支持的核心操作主要有下面几个：插入、删除、查找、按照区间查找数据和迭代输出有序序列。其中，插入、删除、查找以及迭代输出有序序列这几个操作，红黑树也可以完成，时间复杂度跟跳表是一样的。但是，按照区间来查找数据这个操作，红黑树的效率没有跳表高。

---

 ## 6.2 哈希表
 1. 散列冲突
 > **开放寻址法**
 > 开放寻址法的核心思想是，如果出现了散列冲突，我们就重新探测一个空闲位置，将其插入。重新探测的方法：线性探测（在冲突后一个位置）、二次探测（在冲突后两个位置）和双重散列（换个hash函数）。**缺点**：删除数据很麻烦，需要特殊标记已经删除掉的数据。不管采用哪种探测方法，当散列表中空闲位置不多的时候，散列冲突的概率就会大大提高。**优势**：开放寻址法不像链表法，需要拉很多链表。散列表中的数据都存储在数组中，可以有效的抵用CPU缓存加快查询速度。而且这种方法实现的散列表，序列化起来比较简单。链表法因为有指针，所以序列化起来就没那么容易。**总结**：当数据量比较小、装载因子小的时候，适合采用开放寻址法。这也是Java中的ThreadLoaclMap使用开放寻址法解决散列冲突的原因。
 > **链表法**
 > 链表法是一种更加常用的散列冲突解决方法，相比开放寻址法，它要简单很多。所有hash值相同的元素都放到相同槽位对应的链表中。**优势**：对内存的利用率比开放寻址法要高，对大装载因子的容忍度更高
 2. Word文档中单词拼写检查功能是如何实现的？
 A：常用的单词有20万个，平均一个单词占用10个字节的内存空间，那20万英文单词大约占2MB的存储空间，就算放大10倍也就是20MB，所以我们可以用散列表来存储整个英文单词词典。
3. 如何避免低效地扩容？
> 为了解决一次性扩容耗时过多的情况，我们可以将扩容操作穿插在插入操作的过程中，分批完成。当装载因子触达阈值之后，我们只申请新空间，但并不将老的数据搬移到新散列表中。当有新数据要插入时，我们将新数据插入散列表，并且从老的散列表中拿出一个数据放入到新散列表。每次插入一个数据到散列表，我们都重复上面的过程。经过多次插入操作之后，老的散列表中的数据就一点一点全部搬移到新散列表中了。这样没有了集中的一次性数据搬移，插入操作就变的很快了。
4. 装载因子=填入表中的元素个数/散列表的长度
---
## 6.3 深度和广度搜索算法
1. 深度优先搜索算法的时间复杂度是O(E)，E表示边的个数。空间复杂度为O（V），V为顶点的个数。
2. 广度优先搜索算法的的时间复杂度是O(E+V)，空间复杂度为O(V)。

## 6.4 遍历模板
1. 数组
```c++
void traverse(vector<int>& nums)
{
	for(int i=0;i<nums.size();i++)
	{
		nums[i];
	}
}
```
2. 链表
```c++
void traverse(Linkedlist* head)
{	
	head;
	if(head->next)	traverse(head->next);
}
```
3. 二叉树
```c++
void traverse(Tree* root)
{
	//前向遍历
	traverse(root->left);
	//中向遍历
	traverse(root->right);
	//后向遍历
}
```

---
# 7 python
## 7.1 正则表达式
```python
#!/usr/bin/python
# coding=utf-8
import os
import shutil
import sys
import re

result = re.match(reg_pattern,Astr)
if result is None:
    print 1
print result.group(1)
print result.group(3)
print result.group(4)
spfd=open('data.csv', mode='a')
with open('sp.txt') as f:
    for line in f:
        line=line.strip('\n')
        reg_pattern = r'.*BusinessSessionID:<(.*)>.*RTSPCreateLinkTime:<(.*)>.*DescribeRequestTime:<(.*) ms>.*DescribeResonseTime:<(.*) ms>.*SetupRequestTime:<(.*) ms>.*SetupResponseTime:<(.*) ms>    .*PlayRequesetTime:<(.*) ms>.*PlayResponseTime:<(.*) ms>.*FirstPackTime:<(.*) ms>.*Pat/PmtTime:<(.*) ms>.*I-Frame-StartTime:<(.*) ms>.*IFrame-TailTime:<(.*) ms>.*TotalTime:<(.*) ms>.*'
        result = re.match(reg_pattern,line)
        data=result.group(1)
        for i in range(2,14):
            data = data+','+result.group(i)
        data = data +'\n'
        spfd.write(data)
```
