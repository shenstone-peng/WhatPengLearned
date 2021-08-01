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
