C++多线程

# 与 C++11 多线程相关的头文件

C++11 新标准中引入了五个头文件来支持多线程编程，它们分别是 `<atomic>, <thread>, <mutex>, <condition_variable>` 和 `<future>`。

- `<atomic>`：该头文主要声明了两个类, `std::atomic` 和 `std::atomic_flag`，另外还声明了一套 C 风格的原子类型和与 C 兼容的原子操作的函数。

- `<thread>`：该头文件主要声明了 `std::thread` 类，另外 `std::this_thread` 命名空间也在该头文件中。

- `<mutex>`：该头文件主要声明了与互斥量(Mutex)相关的类，包括 `std::mutex_*` 一系列类，`std::lock_guard`, `std::unique_lock`, 以及其他的类型和函数。

- `<condition_variable>`：该头文件主要声明了与条件变量相关的类，包括 `std::condition_variable` 和 `std::condition_variable_any`。

- `<future>`：该头文件主要声明了 `std::promise`, `std::package_task` 两个 Provider 类，以及 `std::future` 和 `std::shared_future` 两个 Future 类，另外还有一些与之相关的类型和函数，`std::async()` 函数就声明在此头文件中。

  > The standard library provides facilities to obtain values that are returned and to catch exceptions that are thrown by asynchronous tasks (i.e. functions launched in separate threads). These values are communicated in a *shared state*, in which the asynchronous task may write its return value or store an exception, and which may be examined, waited for, and otherwise manipulated by other threads that hold instances of [std::future](https://en.cppreference.com/w/cpp/thread/future) or [std::shared_future](https://en.cppreference.com/w/cpp/thread/shared_future) that reference that shared state.

| class                                                        | introduction                                                 |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [promise](https://en.cppreference.com/w/cpp/thread/promise)(C++11) | stores a value for asynchronous retrieval (class template)   | 存储用于异步检索的值<br/>（类模板）                          |
| [packaged_task](https://en.cppreference.com/w/cpp/thread/packaged_task)(C++11) | packages a function to store its return value for asynchronous retrieval (class template) | 打包一个函数以存储其返回值以进行异步检索<br/>（类模板）      |
| [future](https://en.cppreference.com/w/cpp/thread/future)(C++11) | waits for a value that is set asynchronously (class template) | 等待异步设置的值<br/>（类模板）                              |
| [shared_future](https://en.cppreference.com/w/cpp/thread/shared_future)(C++11) | waits for a value (possibly referenced by other futures) that is set asynchronously (class template) | 等待异步设置的值（可能由其他future引用）<br/>（类模板）      |
| [async](https://en.cppreference.com/w/cpp/thread/async)(C++11) | runs a function asynchronously (potentially in a new thread) and returns a [std::future](https://en.cppreference.com/w/cpp/thread/future) that will hold the result (function template) | 异步运行一个函数（可能在新线程中）并返回将保存结果 的[std :: future](https://en.cppreference.com/w/cpp/thread/future) （函数模板） |
| [launch](https://en.cppreference.com/w/cpp/thread/launch)(C++11) | specifies the launch policy for [std::async](https://en.cppreference.com/w/cpp/thread/async) (enum) | 指定[std :: async](https://en.cppreference.com/w/cpp/thread/async) 的启动策略<br/>（枚举） |
| [future_status](https://en.cppreference.com/w/cpp/thread/future_status)(C++11) | specifies the results of timed waits performed on [std::future](https://en.cppreference.com/w/cpp/thread/future) and [std::shared_future](https://en.cppreference.com/w/cpp/thread/shared_future) (enum) | 指定在[std :: future](https://en.cppreference.com/w/cpp/thread/future)和[std :: shared_future](https://en.cppreference.com/w/cpp/thread/shared_future) 上执行的定时等待的结果<br/>（枚举） |



# Mutex

mutex是锁，lock是具体锁，mutex在lock基础上实现lock锁

```c++
namespace std {
    
    class mutex; //互斥锁
    class recursive_mutex; //互斥重复加锁
    class timed_mutex;//互斥定时锁
    class recursive_timed_mutex; //互斥重复定时加锁

    struct defer_lock_t { };
    struct try_to_lock_t { };
    struct adopt_lock_t { };

    constexpr defer_lock_t defer_lock { };
    constexpr try_to_lock_t try_to_lock { };
    constexpr adopt_lock_t adopt_lock { };

    template <class Mutex> class lock_guard;//互斥量
    template <class Mutex> class unique_lock;

    template <class Mutex>
    void swap(unique_lock<Mutex>& x, unique_lock<Mutex>& y);

    ///获得锁
    template <class L1, class L2, class... L3> int try_lock(L1&, L2&, L3&...);
    template <class L1, class L2, class... L3> void lock(L1&, L2&, L3&...);

    struct once_flag {
        constexpr once_flag() noexcept;
        once_flag(const once_flag&) = delete;
        once_flag& operator=(const once_flag&) = delete;
    };

    template<class Callable, class ...Args>
    void call_once(once_flag& flag, Callable func, Args&&... args);
}
```

typedef 原名 新名

 C++中的explicit关键字只能用于修饰只有一个参数的类构造函数, 它的作用是表明该构造函数是显示的, 而非隐式的, 跟它相对应的另一个关键字是implicit, 意思是隐藏的,类构造函数默认情况下即声明为implicit(隐式).

**explicit关键字的作用就是防止类构造函数的隐式自动转换.**

**explicit关键字只对有一个参数的类构造函数有效, 如果类构造函数参数大于或等于两个时, 是不会产生隐式转换的, 所以explicit关键字也就无效了**

**一个例外, 就是当除了第一个参数以外的其他参数都有默认值的时候, explicit关键字依然有效, 此时, 当调用构造函数时只传入一个参数, 等效于只有一个参数的类构造函数**

```c++
class CxString  // 没有使用explicit关键字的类声明, 即默认为隐式声明 
{  
public:  
    char *_pstr;  
    int _size;  
    CxString(int size)  
    {  
        _size = size;                // string的预设大小  
        _pstr = malloc(size + 1);    // 分配string的内存  
        memset(_pstr, 0, size + 1);  
    }  
    CxString(const char *p)  
    {  
        int size = strlen(p);  
        _pstr = malloc(size + 1);    // 分配string的内存  
        strcpy(_pstr, p);            // 复制字符串  
        _size = strlen(_pstr);  
    }  
    // 析构函数这里不讨论, 省略...  
};  
  
    // 下面是调用:  
  
    CxString string1(24);     // 这样是OK的, 为CxString预分配24字节的大小的内存  
    CxString string2 = 10;    // 这样是OK的, 为CxString预分配10字节的大小的内存  
    CxString string3;         // 这样是不行的, 因为没有默认构造函数, 错误为: “CxString”: 没有合适的默认构造函数可用  
    CxString string4("aaaa"); // 这样是OK的  
    CxString string5 = "bbb"; // 这样也是OK的, 调用的是CxString(const char *p)  
    CxString string6 = 'c';   // 这样也是OK的, 其实调用的是CxString(int size), 且size等于'c'的ascii码  
    string1 = 2;              // 这样也是OK的, 为CxString预分配2字节的大小的内存  
    string2 = 3;              // 这样也是OK的, 为CxString预分配3字节的大小的内存  
    string3 = string1;        // 这样也是OK的, 至少编译是没问题的, 但是如果析构函数里用free释放_pstr内存指针的时候可能会报错, 完整的代码必须重载运算符"=", 并在其中处理内存释放


CxString string2 = 10; 
CxString string2(10);  
或  
CxString temp(10);  
CxString string2 = temp; 
```





```c++
class CxString  // 使用关键字explicit的类声明, 显示转换  
{  
public:  
    char *_pstr;  
    int _size;  
    explicit CxString(int size)  
    {  
        _size = size;  
        // 代码同上, 省略...  
    }  
    CxString(const char *p)  
    {  
        // 代码同上, 省略...  
    }  
};  
  
    // 下面是调用:  
  
    CxString string1(24);     // 这样是OK的  
    CxString string2 = 10;    // 这样是不行的, 因为explicit关键字取消了隐式转换  
    CxString string3;         // 这样是不行的, 因为没有默认构造函数  
    CxString string4("aaaa"); // 这样是OK的  
    CxString string5 = "bbb"; // 这样也是OK的, 调用的是CxString(const char *p)  
    CxString string6 = 'c';   // 这样是不行的, 其实调用的是CxString(int size), 且size等于'c'的ascii码, 但explicit关键字取消了隐式转换  
    string1 = 2;              // 这样也是不行的, 因为取消了隐式转换  
    string2 = 3;              // 这样也是不行的, 因为取消了隐式转换  
    string3 = string1;        // 这样也是不行的, 因为取消了隐式转换, 除非类实现操作符"="的重载  

```

## 1、std::mutex

```c++
namespace std {
    class mutex {
        public:
            constexpr mutex();
            ~mutex();
            mutex(const mutex&) = delete;//不允许拷贝，不允许move拷贝
            mutex& operator=(const mutex&) = delete;

            void lock();
            bool try_lock() noexcept;
            void unlock() noexcept;

            typedef implementation-defined native_handle_type;
            native_handle_type native_handle();
    };
}
```

- lock()，调用线程将锁住该互斥量。线程调用该函数会发生下面 3 种情况：(1). 如果该互斥量当前没有被锁住，则调用线程将该互斥量锁住，直到调用 unlock之前，该线程一直拥有该锁。(2). 如果当前互斥量被其他线程锁住，则当前的调用线程被阻塞住。(3). 如果当前互斥量被当前调用线程锁住，则会产生死锁(deadlock)。
- unlock()， 解锁，释放对互斥量的所有权。
- try_lock()，尝试锁住互斥量，如果互斥量被其他线程占有，则当前线程也不会被阻塞。线程调用该函数也会出现下面 3 种情况，
  1. 如果当前互斥量没有被其他线程占有，则该线程锁住互斥量，直到该线程调用 unlock 释放互斥量。
  2. 如果当前互斥量被其他线程锁住，则当前调用线程返回 false，而并不会被阻塞掉。
  3. 如果当前互斥量被当前调用线程锁住，则会产生死锁(deadlock)。

![image-20210323095511219](D:\笔记\C++\C++多线程.assets\image-20210323095511219.png)

## 2、std::recursive_mutex

```C++
class _NODISCARD lock_guard { // class with destructor that unlocks a mutex
public:
    using mutex_type = _Mutex;

    explicit lock_guard(_Mutex& _Mtx) : _MyMutex(_Mtx) { // construct and lock
        _MyMutex.lock();
    }

    lock_guard(_Mutex& _Mtx, adopt_lock_t) : _MyMutex(_Mtx) {} // construct but don't lock

    ~lock_guard() noexcept {
        _MyMutex.unlock();
    }

    lock_guard(const lock_guard&) = delete;
    lock_guard& operator=(const lock_guard&) = delete;

private:
    _Mutex& _MyMutex;
};
```





```c++
namespace std {
    class recursive_mutex {
        public:
            recursive_mutex();
            ~recursive_mutex();
            recursive_mutex(const recursive_mutex&) = delete;
            recursive_mutex& operator=(const recursive_mutex&) = delete;

            void lock();
            bool try_lock() noexcept;
            void unlock() noexcept;

            typedef implementation-defined native_handle_type;
            native_handle_type native_handle();
    };
}
```



```c++
class counter {
public:
	void fun1()
	{
		std::lock_guard<std::recursive_mutex> lk(mutex);
		shared = "func1";
		std::cout << "in fun1 , shared variable is now " << shared << '\n';
	}
	void fun2()
	{
		//第一次加锁
		std::lock_guard<std::recursive_mutex> lk(mutex);
		shared = "func2";
		std::cout << "in fun2 , shared variable is now " << shared << '\n';
		//第二次加锁
		fun1();
		std::cout << "back in fun2 , shared variable is " << shared << "\n";
	}
private:
	std::string shared;
	std::recursive_mutex mutex;
	
};

int main()
{
	counter c;
	std::thread t1(&counter::fun1, &c);
	std::thread t2(&counter::fun2, &c);
	t1.join();
	t2.join();
}


	void fun1()
	{
		mutex.lock();
		shared = "func1";
		std::cout << "in fun1 , shared variable is now " << shared << '\n';
		mutex.unlock();
	}
	void fun2()
	{
		//第一次加锁
		mutex.lock();
		shared = "func2";
		std::cout << "in fun2 , shared variable is now " << shared << '\n';
		//第二次加锁
		fun1();
		std::cout << "back in fun2 , shared variable is " << shared << "\n";
		mutex.unlock();
	}
```

## 3、std::timed_mutex

`try_lock_for` 函数接受一个时间范围，表示在这一段时间范围之内线程如果**没有获得锁则被阻塞**住（与 `std::mutex` 的 `try_lock()` 不同，try_lock 如果被调用时没有获得锁则直接返回 `false`），如果在此期间其他线程释放了锁，则该线程可以获得对互斥量的锁，如果超时（即在指定时间内还是没有获得锁），则返回 `false`。【在这个时间段内我一定要得到锁，不然我就阻塞自己】

`try_lock_until` 函数则接受一个时间点作为参数，在指定时间点未到来之前线程如果没有获得锁则被阻塞住，如果在此期间其他线程释放了锁，则该线程可以获得对互斥量的锁，如果超时（即在指定时间内还是没有获得锁），则返回 `false`。【在这个时间点之前我一定要得到锁，不然我就阻塞自己】

```c++
namespace std {
    class timed_mutex {
        public:
            timed_mutex();
            ~timed_mutex();
            timed_mutex(const timed_mutex&) = delete;
            timed_mutex& operator=(const timed_mutex&) = delete;

            void lock();
            bool try_lock();
            template <class Rep, class Period>
            bool try_lock_for(const chrono::duration<Rep, Period>& rel_time) noexcept;
            template <class Clock, class Duration>
            bool try_lock_until(const chrono::time_point<Clock, Duration>& abs_time) noexcept;
            void unlock();

            typedef implementation-defined native_handle_type; 
            native_handle_type native_handle();    
    };
}
```



```c++
#include<iostream>
#include<chrono>
#include<thread>
#include<mutex>

std::timed_mutex mtx;
void fireWorks()
{
	//在200ms内一直等着锁，等不到就输出-，过了200ms之后就阻塞住，不尝试获得锁，等待分配锁
	//200ms内得到锁了，就跳出循环
	//200ms内没得到锁，阻塞在循环结束处，然后等到有锁了在继续往下执行
	while (!mtx.try_lock_for(std::chrono::milliseconds(200)))
		std::cout << "-";

	//得到锁
	std::this_thread::sleep_for(std::chrono::milliseconds(1000));
	std::cout << "*\n";
	mtx.unlock();
}
int main()
{
	std::thread threads[10];
	for (size_t i = 0; i < 10; i++)
	{
		threads[i] = std::thread(fireWorks);
	}

	for (auto& i : threads)
	{
		i.join();
	}
	return 0;
}
```





![image-20210323105036709](D:\笔记\C++\C++多线程.assets\image-20210323105036709.png)

## 4、std::recursive_timed_mutex

## 5、std::lock_guard

```c++
#include<iostream>
#include<thread>
#include<mutex>
#include<stdexcept>

std::mutex mtx;
int count = 10;

void print_even(int x)
{
	if (x % 2 == 0)
		std::cout << x << " is even\n";
	else
		throw(std::logic_error("not even"));

}

void print_thread_id(int id)
{
	try {
		std::lock_guard<std::mutex> lck(mtx);
		for (size_t i = 0; i < 10000000; i++)
		{
			count++;
		}
		
		print_even(id);
	}
	catch (std::logic_error&)
	{
		std::cout << "[exception caught]\n";
	}
}

int main()
{
	std::thread threads[10];
	for (size_t i = 0; i < 10; i++)
	{
		threads[i] = std::thread(print_thread_id, i + 1);
	}
	for (auto& i : threads)
	{
		i.join();
	}
	std::cout << count << std::endl;
	return 0;
}
```



## 6、std::unique_lock

```c++
#include<iostream>
#include<thread>
#include<mutex>

std::mutex mtx;

void print_block(int n, char c)
{
	//std::unique_lock<std::mutex> lck(mtx);
	for (size_t i = 0; i < n;	i++)
	{
		std::cout << c;
	}
	std::cout << '\n';
}
int main()
{
	std::thread t1(print_block, 5000, '*');
	std::thread t2(print_block, 5000, '$');
	t1.join();
	t2.join();
	return 0;
}
```

## 锁类型

```
template <class Mutex> class lock_guard;
```

### std::lock_guard

通常用于管理某个锁(Lock)对象，因此与 Mutex RAII 相关，方便线程对互斥量上锁。

在某个 `lock_guard` 对象的声明周期内，它所管理的锁对象会一直保持上锁状态；而 `lock_guard` 的生命周期结束之后，它所管理的锁对象会被解锁。

**template \<class Mutex>**

|                            |      |
| -------------------------- | ---- |
| std::mutex                 |      |
| std::recursive_mutex       |      |
| std::timed_mutex           |      |
| std::recursive_timed_mutex |      |
| std::unique_lock           |      |

**锁类型**

| 类型          | 操作                                                         |
| ------------- | ------------------------------------------------------------ |
| BasicLockable | lock` 和 `unlock                                             |
| Lockable      | lock`，`unlock` 和 `try_lock                                 |
| TimeLockable  | `lock`, `unlock`, `try_lock`, `try_lock_for`, `try_lock_until`)。 |

#### **构造函数**

| locking (1)       | explicit lock_guard(mutex_type& m);          |
| ----------------- | -------------------------------------------- |
| adopting (2)      | lock_guard(mutex_type& m, adopt_lock_t tag); |
| copy [deleted](3) | lock_guard(const lock_guard&) = delete;      |

1. locking 初始化，`lock_guard` 对象管理 Mutex 对象 m，并在构造时对 m 进行上锁（调用 `m.lock()`）。
2. adopting 初始化，`lock_guard` 对象管理 Mutex 对象 m，与 locking 初始化(1) 不同的是， Mutex 对象 m 已被当前线程锁住。
3. 拷贝构造，`lock_guard` 对象的拷贝构造和移动构造(move construction)均被禁用，因此 `lock_guard` 对象不可被拷贝构造或移动构造。

**adopt_lock_t tag**

|                                                  |                                                              |
| ------------------------------------------------ | ------------------------------------------------------------ |
| std::adopt_lock_t<br />struct adopt_lock_t {};   | constexpr adopt_lock_t adopt_lock {};<br />使object不要锁互斥量，并且假定互斥量已经被当前线程锁住  给lock_guard用 |
| std::defer_lock_t<br />struct defer_lock_t {};   | constexpr defer_lock_t defer_lock {};<br />使它不要在构建阶段自动的锁定metex lock |
| std::try_to_lock_t<br />struct try_to_lock_t {}; | constexpr try_to_lock_t try_to_lock {};<br />使得它调用try_lock成员函数，而不是lock函数图lock the mutex object |



### std::unique_lock

顾名思义，`unique_lock` 对象以独占所有权的方式（ unique owership）管理 mutex 对象的上锁和解锁操作，所谓独占所有权，就是没有其他的 `unique_lock` 对象同时拥有某个 mutex 对象的所有权。

`unique_lock` 对象同样也不负责管理 Mutex 对象的生命周期，`unique_lock` 对象只是简化了 Mutex 对象的上锁和解锁操作，方便线程对互斥量上锁，即在某个 `unique_lock` 对象的声明周期内，它所管理的锁对象会一直保持上锁状态；而 `unique_lock` 的生命周期结束之后，它所管理的锁对象会被解锁，这一点和 `lock_guard` 类似，但 `unique_lock` 给程序员提供了更多的自由

和 `lock_guard` 一样，这也是一种简单而又安全的上锁和解锁方式，尤其是在程序抛出异常后先前已被上锁的 Mutex 对象可以正确进行解锁操作，极大地简化了程序员编写与 Mutex 相关的异常处理代码。

#### 构造函数

| default (1)        | unique_lock() noexcept;                                      |
| ------------------ | ------------------------------------------------------------ |
| locking (2)        | explicit unique_lock(mutex_type& m);  //没有隐式转换         |
| try-locking (3)    | unique_lock(mutex_type& m, try_to_lock_t tag);               |
| deferred (4)       | unique_lock(mutex_type& m, defer_lock_t tag) noexcept;       |
| adopting (5)       | unique_lock(mutex_type& m, adopt_lock_t tag);                |
| locking for (6)    | template <class Rep, class Period> unique_lock(mutex_type& m, const chrono::duration<Rep,Period>& rel_time); |
| locking until (7)  | template <class Clock, class Duration> unique_lock(mutex_type& m, const chrono::time_point<Clock,Duration>& abs_time); |
| copy [deleted] (8) | unique_lock(const unique_lock&) = delete;                    |
| move (9)           | unique_lock(unique_lock&& x);                                |

- (1) 默认构造函数，新创建的 `unique_lock` 对象不管理任何 Mutex 对象。
- (2) locking 初始化，新创建的 `unique_lock` 对象管理 Mutex 对象 m，并尝试调用 `m.lock()` 对 Mutex 对象进行上锁，如果此时另外某个 `unique_lock` 对象已经管理了该 Mutex 对象 m，则当前线程将会被阻塞。
- (3) try-locking 初始化，新创建的 `unique_lock` 对象管理 Mutex 对象 m，并尝试调用 `m.try_lock()` 对 Mutex 对象进行上锁，**但如果上锁不成功，并不会阻塞当前线程**。
- (4) deferred 初始化，新创建的 `unique_lock` 对象管理 Mutex 对象 m，但是在初始化的时候并不锁住 Mutex 对象。 **m 应该是一个没有当前线程锁住的 Mutex 对象**。
- (5) adopting 初始化，新创建的 `unique_lock` 对象管理 Mutex 对象 m， **m 应该是一个已经被当前线程锁住的 Mutex 对象**。(并且当前新创建的 `unique_lock` 对象拥有对锁(Lock)的所有权)。
- (6) locking 一段时间(duration)，新创建的 `unique_lock` 对象管理 Mutex 对象 m，并试图通过调用 `m.try_lock_for(rel_time)` 来锁住 Mutex 对象一段时间(rel_time)。
- (7) locking 直到某个时间点(time point)，新创建的 `unique_lock` 对象管理 Mutex 对象m，并试图通过调用 `m.try_lock_until(abs_time)` 来在某个时间点(`abs_time`)之前锁住 Mutex 对象。
- (8) 拷贝构造 [被禁用]，unique_lock 对象不能被拷贝构造。
- (9) 移动(move)构造，新创建的 `unique_lock` 对象获得了由 x 所管理的 Mutex 对象的所有权(包括当前 Mutex 的状态)。调用 move 构造之后， x 对象如同通过默认构造函数所创建的，就不再管理任何 Mutex 对象了。

```c++
#include<iostream>
#include<thread>
#include<mutex>
std::mutex foo, bar;
void task_a()
{
	std::lock(foo, bar);//simultaneous lock
	std::unique_lock<std::mutex> lck1(foo, std::adopt_lock);
	std::unique_lock<std::mutex> lck2(bar, std::adopt_lock);
	std::cout << "task a\n";
	// (unlocked automatically on destruction of lck1 and lck2)
}
void task_b()
{
	//foo.lock();
	//bar.lock();
	//D:\a01\_work\4\s\src\vctools\crt\github\stl\src\mutex.cpp(64): mutex destroyed while busy 应该是一个没有被上锁的mutex
	std::unique_lock<std::mutex> lck1(foo, std::defer_lock);
	std::unique_lock<std::mutex> lck2(bar, std::defer_lock);
	//std::lock(lck1, lck2);
	std::cout << "task b\n";
	// (unlocked automatically on destruction of lck1 and lck2)
}
int main()
{
	std::thread th1(task_a);
	std::thread th2(task_b);
	th1.join();
	th2.join();
	return 0;
}
```

#### 移动赋值 move assignment

`std::unique_lock` 支持移动赋值(move assignment)，但是普通的赋值被禁用了，

| move (1)           | unique_lock& operator= (unique_lock&& x) noexcept;    |
| ------------------ | ----------------------------------------------------- |
| copy [deleted] (2) | unique_lock& operator= (const unique_lock&) = delete; |

```c++
std::mutex mtx;           // mutex for critical section
void print(char c)
{
	std::unique_lock<std::mutex> lck;
	lck = std::unique_lock<std::mutex>(mtx);
	for (size_t i = 0; i < 50; i++)
	{
		std::cout << c;
	}
	std::cout << "\n";
}

int main()
{
	std::thread th1(print, '*');//线程1持有mtx，保存mtx状态。mtx结束之后调用unlock
	std::thread th2(print, '$');//线程2持有mtx，

	th1.join();
	th2.join();
	return 0;
}
```

#### std::unique_lock::lock

上锁操作，调用它所管理的 Mutex 对象的 `lock` 函数。如果在调用 Mutex 对象的 lock 函数时该 Mutex 对象已被另一线程锁住，则当前线程会被阻塞，直到它获得了锁。

如果上锁操作失败，则抛出 `system_error` 异常。

```c++
   unique_lock(_Mutex& _Mtx, defer_lock_t) noexcept
        : _Pmtx(_STD addressof(_Mtx)), _Owns(false) {} // construct but don't lock

    ~unique_lock() noexcept {
        if (_Owns) {
            _Pmtx->unlock();
        }
    }
  std::unique_lock<std::mutex> lck (mtx,std::defer_lock);
  // critical section (exclusive access to std::cout signaled by locking lck):
  lck.lock();
  lck.unlock();
```

**解释一下：**

unique_lock里的两个构造函数如下

```c++
    _NODISCARD_CTOR unique_lock(_Mutex& _Mtx, adopt_lock_t)
        : _Pmtx(_STD addressof(_Mtx)), _Owns(true) {} // construct and assume already locked

    unique_lock(_Mutex& _Mtx, defer_lock_t) noexcept
        : _Pmtx(_STD addressof(_Mtx)), _Owns(false) {} // construct but don't lock

_Owns(false)
```

unlock函数如下

```c++
    void unlock() {
        if (!_Pmtx || !_Owns) {
            _Throw_system_error(errc::operation_not_permitted);
        }

        _Pmtx->unlock();
        _Owns = false;
    }
如果pmtx为空或者owns是false就没有权力去执行
    所以如果构造的时候使用defer_lock_t ，那么owns=false，这时候是不可以unlock的
```

再看一下lock，一旦lock了就会设置owns

```c++
    void lock() { // lock the mutex
        _Validate();
        _Pmtx->lock();
        _Owns = true;
    }
```

看一下validate：检查能不能被locked,如果使用的是adopt_lock_t，owns=true 表示已经上锁了。

```c++
    void _Validate() const { // check if the mutex can be locked
        if (!_Pmtx) {
            _Throw_system_error(errc::operation_not_permitted);
        }

        if (_Owns) {
            _Throw_system_error(errc::resource_deadlock_would_occur);
        }
    }
```

#### std::unique_lock::try_lock

```c++
std::mutex mtx;           // mutex for critical section

void print_star(int i) {
    std::unique_lock<std::mutex> lck(mtx, std::defer_lock);
    if (i < 200)
    {
        lck.lock();
        std::cout << "x";
        lck.unlock();
        return;

    }
    if (lck.try_lock())
    {
        std::cout << "*";
    }
    else
    {
        std::cout << "x";
    }
}

int main()
{
    std::vector<std::thread> threads;
    for (size_t i = 0; i < 500; i++)
    {
        threads.emplace_back(print_star,i);
    }

    for (auto& i : threads)
    {
        i.join();
    }

    return 0;
}
```



- C++11新特性

  emplace_back：这个元素原地构造，不需要触发拷贝构造和转移构造。而且调用形式更加简洁，直接根据参数初始化临时对象的成员。

  新版本的原型展示： 

  void push_back(const value_type& x); 

  void push_back(value_type&& x);

   <typename... Args> reference 
  emplace_back(Args&&... args); 

  两者区别：push_back传入一个事先存在的元素对象，调用的是拷贝或移动构造来生成这个新压入的元素对象：construct(\*des, class_name&|&& x)） 

  emplace_back：多个事先存在的对象，调用示义：construct(*des, other_type &x|&&x, type_name &|&&,...) 

  emplace_back传参不定，编译器需要在调用时才生成具体的实现，push_back只是emplace_back的两个偏移化版本！

   push_back只能用类中的拷贝或移动构造，而emplace_back还可以是类中的其他多参数的构造函数，这是优点也是缺点（代码翻倍）

  &&在普通函数中作为参数时，是万能引用，并不是右值引用，在函数体中会使用forward完美转发，调用时是复制或移动和你传的值有关，你传左值它就用左值版本，传右值就用右值版本，心里要有数。

  如果一个类没有上诉的拷贝或移动构造，则不能用于STL容器中，如果没有相应参数类型的构造实现，emplace_back编译不过，找不到它需要的对应构造函数.. 

  move和copy构造唯一区别：move时，指针属性只是简单的拷贝指针【临时变量】，而copy中，指针属性被拷贝的同时，它所指的具体内容也还需要深度copy下去...我们在实现类的两个构造时特别要注意这一点，move中要将旧对象的指针属性置nullptr，这意味着相应的对象不再适合使用了（它必须是临时对象的原因！转移后，内部的指针属性失效！）

  move和copy的性能对比：正如上说，move只是指针优化，如果类本身没有指针属性，则它不需要move，我们也不必强制move不可，copy和move在内置类型和简单类型（指无指针属性、即构造时不需要new）没区别

#### std::unique_lock::try_lock_for

上锁操作，调用它所管理的 Mutex 对象的 `try_lock_until` 函数，如果上锁成功，则返回 true，否则返回 false。过了200s还没有阻塞

```c++
std::timed_mutex mtx;

void fireWorks(int i)
{
	std::unique_lock<std::timed_mutex> lck(mtx, std::defer_lock);


	while (!lck.try_lock_for(std::chrono::milliseconds(200)))
	{
		std::cout << "-";
	}
	
	std::this_thread::sleep_for(std::chrono::milliseconds(1000));
	//lck2.unlock();
	std::cout << "*\n";
	
}


int main()
{
	std::thread threads[10];
	for (size_t i = 0; i < 10; i++)
	{
		threads[i] = std::thread(fireWorks,i);
	}
	for (auto& i : threads)
	{
		i.join();
	}
	return 0;
}
```



#### std::unique_lock::release

只释放所有权，但是不解锁，所以不执行p_mtx->unlock() 会阻塞住

```c++
std::mutex mtx;
int count = 0;

void print_count_and_unlock(std::mutex* p_mtx)
{
	std::cout << "count: " << count << '\n';
	//p_mtx->unlock();
}
//如源码所示，只释放所有权，但是不解锁，所以不执行p_mtx->unlock() 会阻塞住
//_Mutex* release() noexcept {
//	_Mutex* _Res = _Pmtx;
//	_Pmtx = nullptr;
//	_Owns = false;
//	return _Res;
//}
void task()
{
	std::unique_lock<std::mutex> lck(mtx);
	++count;
	//返回指向它所管理的 Mutex 对象的指针，并释放所有权。
	print_count_and_unlock(lck.release());
}

int main()
{
	std::vector<std::thread> threads;
	for (int i = 0; i < 10; ++i)
		threads.emplace_back(task);

	for (auto& x : threads) x.join();

	return 0;
}
```

#### std::unique_lock::owns_lock

返回当前 `std::unique_lock` 对象是否获得了锁。

```C++
std::mutex mtx;           // mutex for critical section

void print_star () {
  std::unique_lock<std::mutex> lck(mtx,std::try_to_lock);
  // print '*' if successfully locked, 'x' otherwise: 
  if (lck.owns_lock())
    std::cout << '*';
  else                    
    std::cout << 'x';
}

int main ()
{
  std::vector<std::thread> threads;
  for (int i=0; i<500; ++i)
    threads.emplace_back(print_star);

  for (auto& x: threads) x.join();

  return 0;
}
```

##### `std::unique_lock::operator bool()`

与 `owns_lock` 功能相同，返回当前 `std::unique_lock` 对象是否获得了锁。**仿函数**

```c++
void print_star () {
  std::unique_lock<std::mutex> lck(mtx,std::try_to_lock);
  // print '*' if successfully locked, 'x' otherwise: 
  if (lck)
    std::cout << '*';
  else                    
    std::cout << 'x';
}
```

#### std::unique_lock::mutex

返回当前std::unique_lock对象管理的Mutex对象的指针



```c++
class MyMutex :public  std::mutex
{
	int _id;
public:
	MyMutex(int id):_id(id){}
	int getid() { return _id; }
};
MyMutex mtx(101);

void print_ids(int id)
{
	std::unique_lock<MyMutex> lck(mtx);
	std::cout << "thread # " << id << " locked mutex " << lck.mutex()->getid() << '\n';
}

int main()
{
	std::thread threads[10];
	for (size_t i = 0; i < 10; i++)
	{
		threads[i] = std::thread(print_ids, i + 1);
	}
	for (auto& i : threads)
		i.join();
	return 0;
}
```



```
thread # 5 locked mutex 101
thread # 1 locked mutex 101
thread # 2 locked mutex 101
thread # 3 locked mutex 101
thread # 4 locked mutex 101
thread # 6 locked mutex 101
thread # 7 locked mutex 101
thread # 8 locked mutex 101
thread # 9 locked mutex 101
thread # 10 locked mutex 101
用的都是101对象
```

# Condition-variable

condition-variable头文件

```c++
namespace std {
    class condition_variable;
    class condition_variable_any;
    void notify_all_at_thread_exit(condition_variable& cond, unique_lock<mutex> lk);//方法
    enum class cv_status { no_timeout, timeout };//枚举类
}    
```

condition_variable和condition_variable_any的区别是wait里传的锁对象。

`std::condition_variable` 对象通常使用 `std::unique_lock<std::mutex>` 来等待，如果需要使用另外的 `lockable` 类型，可以使用 `std::condition_variable_any` 类，

## 1、condition_variable

当 `std::condition_variable` 对象的某个 `wait` 函数被调用的时候

它使用 `std::unique_lock`(封装 `std::mutex`) 来锁住当前线程。

当前线程会一直被阻塞，直到另外一个线程在相同的 `std::condition_variable` 对象上调用了 `notification` 函数来唤醒当前线程。

```c++
namespace std {
    class condition_variable {
        public:    
            condition_variable();
            ~condition_variable();
            condition_variable(const condition_variable&) = delete;
            condition_variable& operator=(const condition_variable&) = delete;

            void notify_one() noexcept;//唤醒一个
            void notify_all() noexcept;//唤醒所有
            void wait(unique_lock<mutex>& lock);//等待
            template <class Predicate>
            void wait(unique_lock<mutex>& lock, Predicate pred);

            template <class Clock, class Duration>
            cv_status wait_until(unique_lock<mutex>& lock,
                const chrono::time_point<Clock, Duration>& abs_time);//等待要时间

            template <class Clock, class Duration, class Predicate>
            bool wait_until(unique_lock<mutex>& lock,
                const chrono::time_point<Clock, Duration>& abs_time,
                Predicate pred);

            template <class Rep, class Period>
            cv_status wait_for(unique_lock<mutex>& lock,
                const chrono::duration<Rep, Period>& rel_time);

            template <class Rep, class Period, class Predicate>
            bool wait_for(unique_lock<mutex>& lock,
                const chrono::duration<Rep, Period>& rel_time,
                Predicate pred);
                
            typedef implementation-defined native_handle_type;
            native_handle_type native_handle();
    };
}
```

### 1.1 构造函数

| default (1)        | condition_variable();                                    |
| ------------------ | -------------------------------------------------------- |
| copy [deleted] (2) | condition_variable (const condition_variable&) = delete; |

### 1.2 wait()

| unconditional (1) | void wait (unique_lock<mutex>& lck);                         |
| ----------------- | ------------------------------------------------------------ |
| predicate (2)     | `template <class Predicate> void wait (unique_lock<mutex>& lck, Predicate pred); ` |

调用wait，线程立刻释放锁，lck.unlock()   别的线程notify它之后，执行lck.lock() 争取获得锁，回到就绪状态争取锁

```c++
    void notify_all() noexcept { // wake up all waiters
        _Cnd_broadcast(_Mycnd());
    }
```



设置了pred【预测条件】，当pred条件为false才wait()当前线程   并且收到其他线程notify之后只有pred为ture才能唤醒

```c++
    template <class _Predicate>
    void wait(unique_lock<mutex>& _Lck, _Predicate _Pred) { // wait for signal and test predicate
        while (!_Pred()) {
            wait(_Lck);
        }
    }
```

### 1.3 wait for()

| unconditional (1) | `template <class Rep, class Period> cv_status wait_for (unique_lock<mutex>& lck,                      const chrono::duration<Rep,Period>& rel_time); ` |
| ----------------- | ------------------------------------------------------------ |
| predicate (2)     | `template <class Rep, class Period, class Predicate> bool wait_for (unique_lock<mutex>& lck,               const chrono::duration<Rep, Period>& rel_time, Predicate pred);` |

 `wait_for` 可以指定一个时间段，在当前线程收到通知或者指定的时间 `rel_time` 超时之前，该线程都会处于阻塞状态。而一旦超时或者收到了其他线程的通知，`wait_for` 返回，剩下的处理步骤和 `wait()` 类似。

阻塞：指定时间内，notify之前

唤醒：超过指定事件，notify之后



## 2、condition_variable_any

```c++
namespace std {
    class condition_variable_any {
        public:
            condition_variable_any();
            ~condition_variable_any();
            condition_variable_any(const condition_variable_any&) = delete;
            condition_variable_any& operator=(const condition_variable_any&) = delete;

            void notify_one() noexcept;
            void notify_all() noexcept;
            template <class Lock>
            void wait(Lock& lock);
            template <class Lock, class Predicate>
            void wait(Lock& lock, Predicate pred);

            template <class Lock, class Clock, class Duration>
            cv_status wait_until(Lock& lock, const chrono::time_point<Clock, Duration>& abs_time);

            template <class Lock, class Clock, class Duration, class Predicate>
            bool wait_until(Lock& lock, const chrono::time_point<Clock, Duration>& abs_time, Predicate pred);

            template <class Lock, class Rep, class Period>
            cv_status wait_for(Lock& lock, const chrono::duration<Rep, Period>& rel_time);

            template <class Lock, class Rep, class Period, class Predicate>
            bool wait_for(Lock& lock, const chrono::duration<Rep, Period>& rel_time, Predicate pred);
    };
}
```

![image-20210324144932591](D:\笔记\C++\C++多线程.assets\image-20210324144932591.png)



![image-20210324145011206](D:\笔记\C++\C++多线程.assets\image-20210324145011206.png)





# future

## 1、future 头文件

```c++
namespace std {
    enum class future_errc {
        broken_promise,
        future_already_retrieved,
        promise_already_satisfied,
        no_state    
    };
    
    enum class launch : unspecified {
        async = unspecified,
        deferred = unspecified,
        implementation-defined
    };
    
    enum class future_status {
        ready,
        timeout,
        deferred
    };
    
    template <> struct is_error_code_enum<future_errc> : public true_type { };
    error_code make_error_code(future_errc e);
    error_condition make_error_condition(future_errc e);

    const error_category& future_category();

    class future_error;

    template <class R> class promise;
    template <class R> class promise<R&>;
    template <> class promise<void>;

    template <class R>
    void swap(promise<R>& x, promise<R>& y);

    template <class R, class Alloc>
    struct uses_allocator<promise<R>, Alloc>;

    template <class R> class future;
    template <class R> class future<R&>;
    template <> class future<void>;
    
    template <class R> class shared_future;
    template <class R> class shared_future<R&>;
    template <> class shared_future<void>;

    template <class> class packaged_task; // undefined
    template <class R, class... ArgTypes>
    class packaged_task<R(ArgTypes...)>;

    template <class R>
    void swap(packaged_task<R(ArgTypes...)&, packaged_task<R(ArgTypes...)>&);

    template <class R, class Alloc>
    struct uses_allocator<packaged_task<R>, Alloc>;

    template <class F, class... Args>
    future<typename result_of<F(Args...)>::type>
    async(F&& f, Args&&... args);

    template <class F, class... Args>
    future<typename result_of<F(Args...)>::type>
    async(launch policy, F&& f, Args&&... args);
}
```

## 2、std::future_error

```c++
namespace std {
    class future_error : public logic_error {
        public:
            future_error(error_code ec); // exposition only
            const error_code& code() const noexcept;
            const char* what() const noexcept;
    };
}
```

继承logic_error

```
class future_error : public logic_error;
```

### 相关枚举类

```
enum class future_errc;
enum class future_status;
enum class launch;
```

#### std::future::errc

| 类型                      | `取值` | 描述                                                         |
| ------------------------- | ------ | ------------------------------------------------------------ |
| broken_promise            | `0`    | 与该 std::future 共享状态相关联的 std::promise 对象在设置值或者异常之前一被销毁。 |
| future_already_retrieved  | `1`    | 与该 std::future 对象相关联的共享状态的值已经被当前 Provider 获取了，即**调用了 std::future::get 函数。** |
| promise_already_satisfied | `2`    | std::promise 对象已经**对共享状态设置了某一值或者异常**。    |
| no_state                  | `3`    | 无共享状态。                                                 |

#### std::future_status

`std::future_status` 类型主要用在 `std::future`(或 `std::shared_future`)中的 `wait_for` 和 `wait_until` 两个函数中的。

| 类型                    | `取值` | 描述                                                         |
| ----------------------- | ------ | ------------------------------------------------------------ |
| future_status::ready    | `0`    | `wait_for`(`或 wait_until`) 因为共享状态的标志变为 ready 而返回。 |
| future_status::timeout  | `1`    | 超时，即 `wait_for`(`或 wait_until`) 因为在指定的时间段（或时刻）内共享状态的标志依然没有变为 `ready` 而返回。 |
| future_status::deferred | `2`    | 共享状态包含了 *deferred* 函数。                             |

#### std::launch

该枚举类型主要是在调用 `std::async` 设置异步任务的启动策略的。

| 类型             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| launch::async    | **Asynchronous:** 异步任务会在另外一个线程中调用，并通过共享状态返回异步任务的结果（一般是调用 std::future::get() 获取异步任务的结果）。 |
| launch::deferred | **Deferred:** **异步任务将会在共享状态被访问时调用**，相当与按需调用（即延迟(deferred)调用）。 |

```c++
#include<iostream>
#include<future>
#include<chrono>
#include<thread>
void print(char c, int ms)
{
	for (int i = 0; i < 10; i++)
	{
		std::this_thread::sleep_for(std::chrono::milliseconds(ms));
		std::cout << c;
	}
}

int main()
{
	std::cout << "with launch::async:\n";
	std::future<void> foo = std::async(std::launch::async, print, '*', 100);//另一个线程马上开始执行
	std::future<void> bar = std::async(std::launch::async, print, '@', 200);
	foo.get();
	bar.get();
	std::cout << "\n\n";

	std::cout << "with launch::deferred:\n";
	foo = std::async(std::launch::deferred,print, '*', 100);//不执行，等get的时候执行
	bar = std::async(std::launch::deferred, print, '@', 200);
	// deferred "get" (perform the actual calls):
	foo.get();
	bar.get();
	std::cout << '\n';
}
```



## 3、std::promise 设置值

```c++
namespace std {
    template <class R>
    class promise {
        public:
            promise();
            template <class Allocator>
            promise(allocator_arg_t, const Allocator& a);
            promise(promise&& rhs) noexcept;
            promise(const promise& rhs) = delete;
            ~promise();

            // assignment
            promise& operator=(promise&& rhs) noexcept;
            promise& operator=(const promise& rhs) = delete;

            void swap(promise& other) noexcept;     //交换promise类

            // retrieving the result
            future<R> get_future();     //通信中介

            // setting the result
            void set_value(see below);   //设置值
            void set_exception(exception_ptr p);

            // setting the result with deferred notification
            void set_value_at_thread_exit(const R& r);
            void set_value_at_thread_exit(see below);
            void set_exception_at_thread_exit(exception_ptr p);
    };
    template <class R>
    void swap(promise<R>& x, promise<R>& y);
    template <class R, class Alloc>
    struct uses_allocator<promise<R>, Alloc>;
}
```

### 构造函数

| default (1)        | promise();                                                   |
| ------------------ | ------------------------------------------------------------ |
| with allocator (2) | template <class Alloc> promise (allocator_arg_t aa, const Alloc& alloc); |
| copy [deleted] (3) | promise (const promise&) = delete;                           |
| move (4)           | promise (promise&& x) noexcept;                              |

1. 默认构造函数，初始化一个空的共享状态。
2. 带自定义内存分配器的构造函数，与默认构造函数类似，但是使用自定义分配器来分配共享状态。
3. 拷贝构造函数，被禁用。
4. 移动构造函数。

另外，`std::promise` 的 `operator=` 没有拷贝语义，即 `std::promise` 普通的赋值操作被禁用，`operator=` 只有 move 语义，所以 `std::promise` 对象是**禁止拷贝= ()**的。

```c++

```



### std::promise::get_future

该函数返回一个与 promise 共享状态相关联的 future 。

返回的 future 对象可以访问由 promise 对象设置在共享状态上的值或者某个异常对象。只能从 promise 共享状态获取一个 future 对象。

在调用该函数之后，promise 对象通常会在某个时间点准备好(**设置一个值或者一个异常对象**)，如果不设置值或者异常，promise 对象在析构时会自动地设置一个 `future_error` 异常(`broken_promise`)来设置其自身的准备状态。上面的例子中已经提到了 `get_future`，此处不再重复。

```c++
void print_int(std::future<int>& fut)
{
	int x = fut.get();
	std::cout << "value:" << x << "\n";
}
//主线程定义一个prom，prom返回的fut状态可以传给另一个线程，另一个线程可以通过fut得到prom的值
int main()
{
	std::promise<int> prom;//
	std::future<int> fut = prom.get_future();//获得关联的future
	std::thread t(print_int, std::ref(fut));//将future交给另一个线程
	prom.set_value(10);
	t.join();
	return 0;
}
```



###  std::promise::set_value 

| generic template (1) | `void set_value (const T& val); void set_value (T&& val); `  |
| -------------------- | ------------------------------------------------------------ |
| specializations (2)  | `void promise<R&>::set_value (R& val);   // when T is a reference type (R&) void promise<void>::set_value (void);   // when T is void` |

设置共享状态的值，此后 promise 的共享状态标志变为 `ready`.

```c++
std::promise<int> prom;

void print_global_promise()
{
	std::future<int> fut = prom.get_future();
	int x = fut.get();
	std::cout << "value:" << x<<"\n";
}

int main()
{
	std::thread th1(print_global_promise);
	prom.set_value(10);
	th1.join();

	prom = std::promise<int>();
	std::thread th2(print_global_promise);
	prom.set_value(20);
	th2.join();

}

=======================================================================================================

void setVal(std::promise<int>& pr)
{
	std::cout << "thread th1 is setting\n";
	pr.set_value(11);
}
int main()
{
	std::promise<int> prom;
	std::future<int> fut = prom.get_future();
	std::thread th1(setVal,std::ref(prom));
	th1.join();
	std::cout << "main thread get value:" << fut.get() << "\n";
}
```





### std::promise::set_exception 

为 promise 对象设置异常，此后 promise 的共享状态变标志变为 `ready`，例子如下，线程1从终端接收一个整数，线程 2 将该整数打印出来，如果线程 1 接收一个非整数，则为 promise 设置一个异常(failbit) ，线程 2 在 `std::future::get` 是抛出该异常。

```c++
#include<iostream>
#include<functional>
#include<thread>  //std::ref
#include<future>
#include<exception>

void get_int(std::promise<int>& prom)
{
	int x;
	std::cout << "Please,enter an integer value:";
	std::cin.exceptions(std::ios::failbit);
	try {
		std::cin >> x;
		prom.set_value(x);
	}
	catch (std::exception&) {
		prom.set_exception(std::current_exception());
	}
}

void print_int(std::future<int>& fut) {
	try {
		int x = fut.get();
		std::cout << "value:" << x << '\n';
	}
	catch (std::exception& e) {
		std::cout << "[exception caught: " << e.what() << "]\n";
	}
}

int main()
{
	std::promise<int> prom;
	std::future<int> fut = prom.get_future();
	std::thread th1(get_int, std::ref(prom));
	std::thread th2(print_int, std::ref(fut));

	th1.join();
	th2.join();

	return 0;
}
```

### std::promise::set_value_at_thread_exit

设置共享状态的值，但是不将共享状态的标志设置为 `ready`，当线程退出时该 promise 对象会自动设置为 ready。

如果某个 `std::future` 对象与该 promise 对象的共享状态相关联，并且该 future 对象正在调用 `get`，则调用 get 的线程会被阻塞，**当线程退出时，调用 `future::get` 的线程解除阻塞**，同时 `get` 返回 `set_value_at_thread_exit` 所设置的值。

注意，该函数已经设置了 promise 共享状态的值，如果在线程结束之前有其他设置或者修改共享状态的值的操作，则会抛出 `future_error`( `promise_already_satisfied` )。

### std::promise::swap

交换 promise 的共享状态。

## 4、std::future  接受值

```c++
namespace std {
    template <class R>
    class future {
        public:
            future();
            future(future &&);
            future(const future& rhs) = delete;
            ~future();

            future& operator=(const future& rhs) = delete;
            future& operator=(future&&) noexcept;
            shared_future<R> share() &&;

            // retrieving the value
            see below get();

            // functions to check state
            bool valid() const;
            void wait() const;
            template <class Rep, class Period>
            future_status wait_for(const chrono::duration<Rep, Period>& rel_time) const;
            template <class Clock, class Duration>
            future_status wait_until(const chrono::time_point<Clock, Duration>& abs_time) const;
    };
}
```

`std::future` 可以用来获取异步任务的结果，因此可以把它当成一种简单的线程间同步的手段。

`std::future` 通常由某个 Provider 创建，你可以把 Provider 想象成一个**异步任务的提供者**，**Provider 在某个线程中设置共享状态的值，与该共享状态相关联的 `std::future` 对象调用 `get`（通常在另外一个线程中） 获取该值，如果共享状态的标志不为 `ready`，则调用 `std::future::get` 会阻塞当前的调用者，直到 Provider 设置了共享状态的值（此时共享状态的标志变为 `ready`）**，`std::future::get` 返回异步任务的值或异常（如果发生了异常）。



一个有效(`valid`)的 `std::future` 对象通常由以下三种 Provider 创建，并和某个共享状态相关联。**Provider 可以是函数或者类**，其实我们前面都已经提到了，他们分别是：

1. `std::async` 函数，本文后面会介绍 `std::async()` 函数。【函数】
2. `std::promise::get_future`，`get_future` 为 promise 类的成员函数，详见 C++11 并发指南四(`<future>` 详解一 `std::promise` 介绍)。【类】
3. `std::packaged_task::get_future`，此时 `get_future` 为 `packaged_task` 的成员函数，详见C++11 并发指南四(`<future>` 详解二 `std::packaged_task` 介绍)。【类】



一个 `std::future` 对象只有在有效(`valid`)的情况下才有用(useful)，由 `std::future` 默认构造函数创建的 `future` 对象不是有效的（除非当前非有效的 `future` 对象被 `move` 赋值另一个有效的 future 对象）。



在一个有效的 `future` 对象上调用 `get` 会阻塞当前的调用者，直到 Provider 设置了共享状态的值或异常（此时共享状态的标志变为 `ready`），`std::future::get` 将返回异步任务的值或异常（如果发生了异常）。

> std::provider::set_value_at_thread_exit  设置值的时候共享状态会变成ready，使用这个方法，共享状态不会变成ready，然后future使用get方法的时候会阻塞
> std::packaged_task::make_ready_at_thread_exit    同理

### demo

只要执行完了task的任务，future就能get，不管别的语句。task包装的任务执行完了，future就能get就不会阻塞了。

```c++
#include<iostream>
#include<future>
#include<chrono>

bool is_prime(int x)//因为这个方法是包装的任务，所以这个方法执行结束后主线程，才能不阻塞。具体看7、make_ready_at_thread_exit
{
	std::this_thread::sleep_for(std::chrono::seconds(1));
	for (int i = 2; i < x; i++)
	{
		if (x % i == 0)
			return false;
		return true;
	}
}

int main()
{
	//std::future<bool> fut = std::async(is_prime, 444444444443);
	std::packaged_task<bool(int)> pro(is_prime);
	std::future<bool> fut = pro.get_future();
	std::thread th(std::move(pro),44443);
	th.detach();
	
	std::cout << "checking,please wait";
	std::chrono::milliseconds span(10);
	while (fut.wait_for(span)==std::future_status::timeout)
	{
		std::cout << ".";
	}
	bool x = fut.get();
	std::cout<<"\n444444443 " << (x ? "is" : "is not") << " prime.\n";
	return 0;
}
```

### 构造函数

`std::future` 一般由 `std::async`, `std::promise::get_future`, `std::packaged_task::get_future` 创建，不过也提供了构造函数，如下表所示：

| default (1)        | future() noexcept;              |
| ------------------ | ------------------------------- |
| copy [deleted] (2) | future(const future&) = delete; |
| move (3)           | future(future&& x) noexcept;    |

另外，`std::future` 的普通赋值操作也被禁用，只提供了 `move` 赋值操作。

```
std::future<int> fut;           // 默认构造函数
fut = std::async(do_some_task);   // move-赋值操作
```

### std::future::share()

返回一个 `std::shared_future` 对象（本文后续内容将介绍 `std::shared_future`），调用该函数之后，该 `std::future` 对象本身已经不和任何共享状态相关联，因此该 `std::future` 的状态不再是 `valid` 的了。

```c++
int do_get_value() { return 10; }

int main()
{
	std::future<int> fut = std::async(do_get_value);
	std::shared_future<int> sf = fut.share();
	
	//fut只能访问一次，sf可多次访问
	std::cout << "value: " << sf.get() << '\n';
	std::cout << "its double: " << sf.get()*2 << '\n';
	return 0;
}
```

### std::future::get()

`std::future::get` 一共有三种形式，如下表所示：

| generic template (1)         | T get();                                                 |
| ---------------------------- | -------------------------------------------------------- |
| reference specialization (2) | R& future<R&>::get(); // when T is a reference type (R&) |
| void specialization (3)      | void future<void>::get(); // when T is void              |

当与该 `std::future` 对象相关联的共享状态标志变为 `ready` 后，调用该函数将返回保存在共享状态中的值，如果共享状态的标志不为 `ready`，则调用该函数会阻塞当前的调用者，而此后一旦共享状态的标志变为 `ready`，`get` 返回 Provider 所设置的共享状态的值或者异常（如果抛出了异常）。



th1 和 th2 同时启动，prom还没有setvalue th2阻塞，setvalue完了，fut获得值

```c++
#include <iostream>       // std::cin, std::cout, std::ios
#include <functional>     // std::ref
#include <thread>         // std::thread
#include <future>         // std::promise, std::future
#include <exception>      // std::exception, std::current_exception

void get_int(std::promise<int>& prom) {
    int x;
    std::cout << "Please, enter an integer value: ";
    std::cin.exceptions (std::ios::failbit);   // throw on failbit
    try {
        std::cin >> x;                         // sets failbit if input is not int
        prom.set_value(x);
    } catch (std::exception&) {
        prom.set_exception(std::current_exception());
    }
}

void print_int(std::future<int>& fut) {
    try {
        int x = fut.get();
        std::cout << "value: " << x << '\n';
    } catch (std::exception& e) {
        std::cout << "[exception caught: " << e.what() << "]\n";
    }
}

int main ()
{
    std::promise<int> prom;
    std::future<int> fut = prom.get_future();

    std::thread th1(get_int, std::ref(prom));
    std::thread th2(print_int, std::ref(fut));

    th1.join();
    th2.join();
    return 0;
}
```

### std::future::valid()

检查当前的 `std::future` 对象是否有效，即释放与某个共享状态相关联。

一个有效的 `std::future` 对象只能通过 `std::async()`, `std::future::get_future` 或者 `std::packaged_task::get_future` 来初始化。

```c++
int do_get_value() { return 11; }

int main()
{
	std::future<int> foo, bar;
	foo = std::async(do_get_value); // foo->valid
	bar = std::move(foo);           //foo->invalid   bar->valid
	if (foo.valid())
		std::cout << "foo value: " << foo.get() << "\n";
	else
		std::cout << "foo invalid!\n";
	if (bar.valid())
		std::cout << "bar value: " << bar.get() << "\n";
	else
		std::cout << "bar invalid!\n";
}
```

### std::future::wait()

等待与当前 `std::future` 对象相关联的共享状态的标志变为 `ready`.

如果共享状态的标志不是 `ready`（此时 Provider 没有在共享状态上设置值（或者异常）），调用该函数会被阻塞当前线程，直到共享状态的标志变为 ready。

一旦共享状态的标志变为 `ready`，`wait()` 函数返回，当前线程被解除阻塞，但是 `wait()` 并不读取共享状态的值或者异常。

- wait  等待变ready
- get   ready的时候读取，没ready等着

```c++
bool prime(int x)
{
	for (int i = 2; i < x; i++)
	{
		if (x % i == 0)
			return false;
		return true;
	}
}

int main()
{
	std::future<bool> fut = std::async(prime, 194232491);
	std::cout << "checking...\n";
	fut.wait();

	std::cout << "\n194232491";
	if (fut.get()) // guaranteed to be ready (and not block) after wait returns  等待返回之后，保证是准备好的
		std::cout << " is prime.\n";
	else
		std::cout << "is not prime.\n";
	return 0;
}
```



### std::future::wait_for()

```
template <class Rep, class Period>
future_status wait_for (const chrono::duration<Rep,Period>& rel_time) const;
```

`wait_for()` 可以设置一个时间段 `rel_time`，时间段内等不到就阻塞，在等待了 `rel_time` 的时间长度后 `wait_for()` 返回，返回值如下：

| 返回值                  | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| future_status::ready    | 共享状态的标志已经变为 ready，即 Provider 在共享状态上设置了值或者异常。 |
| future_status::timeout  | 超时，即在规定的时间内共享状态的标志没有变为 ready。         |
| future_status::deferred | 共享状态包含一个 *deferred* 函数。                           |

```c++
#include<iostream>
#include<future>
#include<chrono>
bool prime(int x)
{
	std::this_thread::sleep_for(std::chrono::seconds(10));
	for (int i = 2; i < x; i++)
	{
		if (x % i == 0)
			return false;
		return true;
	}
}
int main()
{
	std::future<bool> fut = std::async(prime, 194232491);
	std::cout << "Checking\n";
	std::chrono::milliseconds span(1000); // 设置超时间隔.
	//执行wait_for(span) 等待阻塞，等待完了，等式成立。因为while所以再次执行wait_for..... 
	while (fut.wait_for(span)==std::future_status::timeout)
	{
		std::cout << ".";
	}
	std::cout << "\n194232491 ";
	if (fut.get()) // guaranteed to be ready (and not block) after wait returns
		std::cout << "is prime.\n";
	else
		std::cout << "is not prime.\n";

	return 0;
}
```

### std::future::wait_until()

与 `std::future::wait()` 的功能类似，即等待与该 `std::future` 对象相关联的共享状态的标志变为 `ready`，该函数原型如下：

```
template <class Rep, class Period>
future_status wait_until (const chrono::time_point<Clock,Duration>& abs_time) const;
```

而与 `std::future::wait()` 不同的是，`wait_until()` 可以设置一个系统绝对时间点 `abs_time`，如果共享状态的标志在该时间点到来之前没有被 Provider 设置为 `ready`，则调用 `wait_until` 的线程被阻塞，在 `abs_time` 这一时刻到来之后 `wait_until()` 返回，返回值如下：

| 返回值                  | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| future_status::ready    | 共享状态的标志已经变为 ready，即 Provider 在共享状态上设置了值或者异常。 |
| future_status::timeout  | 超时，即在规定的时间内共享状态的标志没有变为 ready。         |
| future_status::deferred | 共享状态包含一个 *deferred* 函数。                           |

## 5、std::shared_future

`std::shared_future` 与 `std::future` 类似，但是 `std::shared_future` 可以拷贝、多个 `std::shared_future` 可以**共享某个共享状态的最终结果**(即共享状态的某个值或者异常)。`shared_future` 可以通过某个 `std::future` 对象隐式转换（参见 `std::shared_future` 的构造函数），或者通过 `std::future::share()` 显示转换，无论哪种转换，被转换的那个 `std::future` 对象都会变为 not-valid.

```c++
namespace std {
    template <class R>
    class shared_future {
        public:
            shared_future() noexcept;
            shared_future(const shared_future& rhs);
            shared_future(future<R>&&) noexcept;
            shared_future(shared_future&& rhs) noexcept;
            ~shared_future();

            shared_future& operator=(const shared_future& rhs);
            shared_future& operator=(shared_future&& rhs);

            // retrieving the value
            see below get() const;

            // functions to check state
            bool valid() const;
            void wait() const;
            template <class Rep, class Period>
            future_status wait_for(const chrono::duration<Rep, Period>& rel_time) const;
            template <class Clock, class Duration>
            future_status wait_until(const chrono::time_point<Clock, Duration>& abs_time) const;
    };
}
```

### 构造函数

`std::shared_future` 共有四种构造函数，如下表所示：

| default (1)          | shared_future() noexcept;                   |
| -------------------- | ------------------------------------------- |
| copy (2)             | shared_future (const shared_future& x);     |
| move (3)             | shared_future (shared_future&& x) noexcept; |
| move from future (4) | shared_future (future<T>&& x) noexcept;     |

最后 `move from future(4)` 即从一个有效的 `std::future` 对象构造一个 `std::shared_future`，构造之后 `std::future` 对象 x 变为无效(not-valid)。

`sf=fut.share()`

### 成员函数

和 `std::future` 大部分相同，如下（每个成员函数都给出了连接）：

- `operator=()`: 赋值操作符，与 std::future 的赋值操作不同，std::shared_future 除了支持 move 赋值操作外，还支持普通的赋值操作。
- `get()`: 获取与该 std::shared_future 对象相关联的共享状态的值（或者异常）。
- `valid()`: 有效性检查。
- `wait()`: 等待与该 std::shared_future 对象相关联的共享状态的标志变为 ready。
- `wait_for()`: 等待与该 std::shared_future 对象相关联的共享状态的标志变为 ready。（等待一段时间，超过该时间段wait_for 返回。）
- `wait_until()`: 等待与该 std::shared_future 对象相关联的共享状态的标志变为 ready。（在某一时刻前等待，超过该时刻 wait_until 返回。）



## 6、std::async

```c++
namespace std {
    template <class F, class... Args>
    future<typename result_of<F(Args...)>::type>
    async(F&& f, Args&&... args);

    template <class F, class... Args>
    future<typename result_of<F(Args...)>::type>
    async(launch policy, F&& f, Args&&... args);
}
```

上面两组 `std::async()` 的不同之处是第一类 `std::async` 没有指定异步任务（即执行某一函数）的**启动策略(launch policy)**，而第二类函数指定了启动策略，详见 `std::launch` 枚举类型，指定启动策略的函数的 policy 参数可以是 `launch::async`，`launch::deferred`，以及两者的按位或( | )。

`std::async()` 的 `fn` 和 `args` 参数用来指定异步任务及其参数。另外，`std::async()` 返回一个 `std::future` 对象，通过该对象可以获取异步任务的值或异常（如果异步任务抛出了异常）。

```c++
#include <stdio.h>
#include <stdlib.h>

#include <cmath>
#include <chrono>
#include <future>
#include <iostream>

double ThreadTask(int n) {
    std::cout << std::this_thread::get_id()
        << " start computing..." << std::endl;

    double ret = 0;
    for (int i = 0; i <= n; i++) {
        ret += std::sin(i);
    }

    std::cout << std::this_thread::get_id()
        << " finished computing..." << std::endl;
    return ret;
}

int main(int argc, const char* argv[])
{
    std::future<double> f(std::async(std::launch::async, ThreadTask, 100000000));

#if 1
    while (f.wait_until(std::chrono::system_clock::now() + std::chrono::seconds(1))
        != std::future_status::ready) {
        std::cout << "task is running......\n";
    }
#else
    while (f.wait_for(std::chrono::seconds(1))
        != std::future_status::ready) {
        std::cout << "task is running...\n";
    }
#endif

    std::cout << f.get() << std::endl;

    return EXIT_SUCCESS;
}
```







## 7、std::packaged_task  [fut get方法返回值]

```c++
namespace std {
    template<class> class packaged_task; // undefined

    template<class R, class... ArgTypes>
    class packaged_task<R(ArgTypes...)> {
        public:
            typedef R result_type;

            // construction and destruction
            packaged_task() noexcept;
            template <class F>
            explicit packaged_task(F f);
            template <class F, class Allocator>
            explicit packaged_task(allocator_arg_t, const Allocator& a, F f);
            explicit packaged_task(R(*f)(ArgTypes...));
            template <class F>
            explicit packaged_task(F&& f);
            template <class F, class Allocator>
            explicit packaged_task(allocator_arg_t, const Allocator& a, F&& f);
            ~packaged_task();

            // no copy
            packaged_task(packaged_task&) = delete;
            packaged_task& operator=(packaged_task&) = delete;

            // move support
            packaged_task(packaged_task&& other) noexcept;
            packaged_task& operator=(packaged_task&& other);
            void swap(packaged_task& other) noexcept;
            bool valid() const noexcept;

            // result retrieval
            future<R> get_future();
            
            // execution
            void operator()(ArgTypes... );
            void make_ready_at_thread_exit(ArgTypes...);
            void reset();
    };

    template <class R, class... ArgTypes>
    void swap(packaged_task<R(ArgTypes...)>& x, packaged_task<R(ArgTypes...)>& y) noexcept;
    template <class R, class Alloc>
    struct uses_allocator<packaged_task<R>, Alloc>;
}
```

`std::packaged_task` 包装一个**可调用的对象**【仿函数】，并且允许异步获取该可调用对象**产生的结果**，从包装可调用对象意义上来讲，`std::packaged_task` 与 `std::function` 类似，只不过 `std::packaged_task` 将其包装的可调用对象的**执行结果传递给一个** `std::future` 对象（该对象通常在另外一个线程中获取 `std::packaged_task` 任务的执行结果）。

`std::packaged_task` 对象内部包含了两个最基本元素，

一、被包装的任务(stored task)，任务(task)是一个可调用的对象，如函数指针、成员函数指针或者函数对象，

二、共享状态(shared state)，用于保存任务的返回值，可以通过 `std::future` 对象来达到异步访问共享状态的效果。

可以通过 `std::packged_task::get_future` 来获取与共享状态相关联的 `std::future` 对象。在调用该函数之后，两个对象共享相同的共享状态，具体解释如下：

1. `std::packaged_task` 对象是异步 Provider，它在某一时刻通过调用被包装的任务来设置共享状态的值。【promise是通过set_value设置值的】
2. `std::future` 对象是一个异步返回对象，通过它可以获得共享状态的值，当然在必要的时候需要等待共享状态标志变为 `ready`.

`std::packaged_task` 的共享状态的生命周期一直持续到最后一个与之相关联的对象被释放或者销毁为止。



```c++
#include<iostream>
#include<future>
#include<chrono>
#include<thread>
int count(int from, int to)
{
	for (int i=from;i!=to ; i--)
	{
		std::cout << i << "\n";
		std::this_thread::sleep_for(std::chrono::seconds(1));
	}
	std::cout << "Finished\n";
	return from - to;
}
int main()
{
	std::packaged_task<int(int, int)> task(count);
	std::future<int> fut = task.get_future();
	std::thread th(std::move(task), 10, 0);
	int value = fut.get();
	std::cout << "The countdown lasted for" << value << " seconds.\n";
	th.join();
	return 0;

	std::packaged_task<int(int,int)> pt;

}
```



### 构造函数

| default (1)        | packaged_task() noexcept;<br />std::packaged_task<int(int,int)> pt; |
| ------------------ | ------------------------------------------------------------ |
| initialization (2) | `template <class Fn> explicit packaged_task (Fn&& fn); `   <br />std::packaged_task<int(int, int)> task(count); |
| with allocator (3) | `template <class Fn, class Alloc> explicit packaged_task (allocator_arg_t aa, const Alloc& alloc, Fn&& fn); ` |
| copy [deleted] (4) | packaged_task (const packaged_task&) = delete;               |
| move (5)           | packaged_task (packaged_task&& x) noexcept;<br />std::thread th(std::move(task), 10, 0); |

1. 默认构造函数，初始化一个空的共享状态，并且该 `packaged_task` 对象**无包装任务**。
2. 初始化一个共享状态，并且被包装任务由参数 fn 指定。
3. 带自定义内存分配器的构造函数，与默认构造函数类似，但是使用自定义分配器来分配共享状态。
4. 拷贝构造函数，被禁用。
5. 移动构造函数。

```c++
#include <iostream>     // std::cout
#include <utility>      // std::move
#include <future>       // std::packaged_task, std::future
#include <thread>       // std::thread

int main ()
{
    std::packaged_task<int(int)> foo; // 默认构造函数.

    // 使用 lambda 表达式初始化一个 packaged_task 对象.
    std::packaged_task<int(int)> bar([](int x){return x*2;});

    foo = std::move(bar); // move-赋值操作，也是 C++11 中的新特性.

    // 获取与 packaged_task 共享状态相关联的 future 对象.
    std::future<int> ret = foo.get_future();

    std::thread(std::move(foo), 10).detach(); // 产生线程，调用被包装的任务.

    int value = ret.get(); // 等待任务完成并获取结果.
    std::cout << "The double of 10 is " << value << ".\n";

    return 0;
}
```

### std::packaged_task::valid

检查当前 `packaged_task` 是否**和一个有效的共享状态相关联**，对于由默认构造函数生成的 `packaged_task` 对象，该函数返回 `false`，除非中间进行了 `move` 赋值操作或者 `swap` 操作。

**默认构造是false，move是false**

```c++
#include<iostream>
#include<utility>
#include<future>
#include<thread>

//在新线程中启动一个int(int) package_task
std::future<int> launcher(std::packaged_task<int(int)>& tsk, int arg)
{
	if (tsk.valid())
	{
		std::cout << tsk.valid();  //1
		std::future<int> ret = tsk.get_future();
		//std::cout << tsk.valid();  //1
		std::thread(std::move(tsk), arg).detach();  //匿名类   开辟线程执行package_task  原来的task执行权归thread了。
		std::cout << tsk.valid();    //0
		std::cout << "\n";
		std::packaged_task<int(int)> temp([](int x) {return x * 2; });
		std::cout << temp.valid();  //1
		temp.swap(tsk);
		std::cout << temp.valid();  //0
		std::cout << tsk.valid();   //1
		return ret;
	}
	else
		return std::future<int>();
}

int main()
{
	//检查当前 packaged_task 是否和一个有效的共享状态相关联，
	//对于由默认构造函数生成的 packaged_task 对象，该函数返回 false，除非中间进行了 move 赋值操作或者 swap 操作。
	//如果里面有方法，future就有效。默认里面没方法，就无效。
	std::packaged_task<int(int)> tsk([](int x) {return x * 2; });
	std::future<int> fut = launcher(tsk, 25);
	std::cout << "The double of 25 is " << fut.get() << ".\n";
	return 0;
}
```



### std::packaged_task::operator()(Args... args)

**调用**该 packaged_task 对象所**包装的对象**(通常为函数指针，函数对象，lambda 表达式等)，传入的参数为 args. 调用该函数一般会发生两种情况：

1. 如果成功调用 `packaged_task` 所包装的对象，则返回值（如果被包装的对象有返回值的话）被保存在 `packaged_task` 的共享状态中。
2. 如果调用 `packaged_task` 所包装的对象失败，并且抛出了异常，则异常也会被保存在 `packaged_task` 的共享状态中。

以上两种情况都使共享状态的标志变为 `ready`，因此其他等待该共享状态的线程可以获取共享状态的值或者异常并继续执行下去。



由于被包装的任务在 `packaged_task` 构造时指定，因此调用 `operator()` 的效果由 packaged_task 对象构造时所指定的可调用对象来决定：

1. 如果被包装的任务是**函数指针或者函数对象**，调用 `std::packaged_task::operator()` 只是将**参数传递**给被包装的对象。
2. 如果被包装的任务是**指向类的非静态成员函数的指针**，那么 `std::packaged_task::operator()` 的第一个参数应该指定为**成员函数被调用的那个对象**，剩余的参数作为该**成员函数的参数**。
3. 如果被包装的任务是**指向类的非静态成员变量**，那么 `std::packaged_task::operator()` **只允许单个参数**。



```c++
int countlettersCallback(int s1, int s2)
{
	return s1 + s2;
	
}

void countletters(std::string s1, std::string s2)
{
	s1.pop_back();
	s2.pop_back();
	std::cout << "Include " << sizeof(s1 + s2) / sizeof(wchar_t) << " letter.\n";
	
}

int main()
{
	
	std::packaged_task<int(int, int)> tsk1(countlettersCallback);
	tsk1(4, 9);
	auto fut1 = tsk1.get_future();
	std::cout << "Include " << fut1.get() << " letters.\n";



	std::packaged_task<void(std::string, std::string)> tsk2(countletters);
	tsk2("北京", "上");
	auto fut2 = tsk2.get_future();
	if (tsk2.valid())
		std::cout << "Shared state is valid.\n";
	return 0;
}
```



### std::packaged_task::make_ready_at_thread_exit

在被调用的线程退出后，将共享状态的标志设置为ready。如果std::future对象尝试使用get()获取共享状态的值（通常是在其他线程里），则会被阻塞直到本线程退出。

detach就是把你扔了，不等你执行完，我执行完你到哪就到哪吧

join就是我要等你执行完，我再继续执行

yield就是我让出我时间片



```c++
int add(int a, int b)
{
	return a + b;
}
void func(packaged_task<int(int, int)>& tsk)
{
	//tsk.make_ready_at_thread_exit(1, 2);
	th(1,2);//只要执行完了task的任务，future就能get，上面这个方法规定必须要此线程结束才能get
	this_thread::sleep_for(chrono::seconds(3));
	cout << "hahah";
}

int main()
{
	packaged_task<int(int, int)> tsk(add);
	future<int> fut=tsk.get_future();
	thread th(func,  ref(tsk));
	th.detach();
	//th.join();
	//cout << fut.get();
	//auto status = fut.wait_for(chrono::seconds(5));
	//if (status == future_status::ready)
	//	cout << fut.get();
	return 0;
}
```



### std::packaged_task::reset()

重置 packaged_task 的共享状态，但是保留之前的被包装的任务。请看例子，该例子中，packaged_task 被重用了多次：

```c++
int main()
{
	std::packaged_task<int(int)> tsk(triple);
	std::future<int> fut = tsk.get_future();
	//std::thread(std::move(tsk), 100).detach();   move之后tsk就置为空了，所以就不能这么写
	tsk(5);
	std::cout << "The triple of 100 is " << fut.get() << ".\n";

	//重置future
	tsk.reset();
	std::cout << tsk.valid();
	fut = tsk.get_future();
	std::thread(std::move(tsk), 200).detach();
	std::cout << "Thre triple of 200 is " << fut.get() << ".\n";

	return 0;
}
```



### std::packaged_task::swap()

交换 `packaged_task` 的共享状态。



### std::package_task::get_future()

```c++
#include<iostream>
#include<utility>
#include<future>
#include<thread>
//int main()
//{
//	std::packaged_task<int(int)> tsk([](int x) {return x * 3; });
//	std::future<int> fut = tsk.get_future();
//	std::thread(std::move(tsk), 50).detach();
//	std::cout << "result is " << fut.get();
//
//}

int triple(int x) { return x * 3; }

int main()
{
	std::packaged_task<int(int)> tsk(triple);
	std::future<int> fut = tsk.get_future();
	//std::thread(std::move(tsk), 100).detach();   move之后tsk就置为空了，所以就不能这么写
	tsk(5);
	std::cout << "The triple of 100 is " << fut.get() << ".\n";

	//重置future
	tsk.reset();
	std::cout << tsk.valid();
	fut = tsk.get_future();
	std::thread(std::move(tsk), 200).detach();
	std::cout << "Thre triple of 200 is " << fut.get() << ".\n";

	return 0;
}
```









同一个condition里放锁：wait到阻塞队列让当前线程释放锁对象，如果是不同的conditon里放同一把锁是不是放弃同一把锁，wait到不同的阻塞队列，然后唤醒不同的阻塞队列，