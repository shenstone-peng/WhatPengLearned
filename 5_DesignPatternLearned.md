# 5 设计模式
## 5.1 单例模式
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
