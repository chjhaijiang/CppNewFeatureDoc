std::future是一个类模板(class template)，其对象存储未来的值，
到底什么是未来的值呢？
事实上，一个std::future对象在内部存储一个将来会被赋值的值，并提供了一个访问该值的机制，通过get()成员函数实现。
但如果有人视图在get()函数可用之前通过它来访问相关的值，那么get()函数将会阻塞，直到该值可用。

std::promise也是一个类模板，其对象有可能在将来对值进行赋值，每个std::promise对象有一个对应的std::future对象，
一旦由std::promise对象设置，std::future将会对其赋值。

std::promise对象与其管理的std::future对象共享数据。

```cpp
#include <iostream>
#include <thread>
#include <future>
#include <chrono>
using namespace std::chrono;

void initiazer(std::promise<int>* promObj) {
	std::cout << "Inside Thread" << std::endl;
	std::this_thread::sleep_until(system_clock::now() + seconds(3));
	promObj->set_value(35);
}

int main() {
	std::promise<int> promiseObj;
	std::future<int> futureObj = promiseObj.get_future();
	std::thread th(initiazer, &promiseObj);
	std::this_thread::sleep_until(system_clock::now() + seconds(1));	
	std::cout << futureObj.get() << std::endl;
	std::cout << "Not executing until future object is gottten!" << std::endl;
	th.join();

	return 0;
}

```
