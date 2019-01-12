---
layout: post
title:  "[C++] thread"
date:   2017-08-24
comments: true
categories: c
tags: thread
description:
published: true
---

c++11 之后在STL中引入了thread


```c++
#include <iostream>
#include <thread>

void foo()
{
	std::cout << "foo thread thread id: " << std::this_thread::get_id() << '\n';
}

void bar(int x)
{
	std::cout << "bar thread thread id: " << std::this_thread::get_id() << '\n';
}

int main()
{
	std::thread first(foo);
	std::thread second(bar, 0);

	std::cout << "main thread id: " << std::this_thread::get_id() << '\n';

	first.join();
	second.join();

	std::cout << "main end\n";

	return 0;
}
```

## get_id()

```c++
#include <iostream>
#include <thread>
#include <chrono>

std::thread::id main_thread_id = std::this_thread::get_id();

void is_main_thread()
{
	if (main_thread_id == std::this_thread::get_id())
		std::cout << "This is the main thread.\n";
	else
		std::cout << "This is no the main thread.\n";
}

int main()
{
	is_main_thread();
	std::thread th(is_main_thread);
	th.join();
	return 0;
}
```


## detach()

```c++
#include <iostream>
#include <thread>
#include <chrono>

void pause_thread(int n)
{
	std::this_thread::sleep_for(std::chrono::seconds(n));
	std::cout << "pause of " << n << " seconds ended\n";
}

int main()
{
	std::cout << "Spawning and detaching 3 threads...\n";
	std::thread(pause_thread, 1).detach();
	std::thread(pause_thread, 2).detach();
	std::thread(pause_thread, 3).detach();
	std::cout << "Done spawning threads.\n";

	std::cout << "(the main thread will now pause for 5 seconds)\n";
	pause_thread(5);
	return 0;
}
/*
output:
Spawning and detaching 3 threads...
Done spawning threads.
(the main thread will now pause for 5 seconds)
pause of 1 seconds ended
pause of 2 seconds ended
pause of 3 seconds ended
pause of 5 seconds ended
*/
```


## joinable()

```c++
#include <iostream>
#include <thread>

void mythread()
{
	std::cout << "mythread\n";
}

int main()
{
	std::thread foo;
	std::thread bar(mythread);

	std::cout << "Joinable after construction:\n" << std::boolalpha;
	std::cout << "foo: " << foo.joinable() << '\n';
	std::cout << "bar: " << bar.joinable() << '\n';

	if (foo.joinable())
		foo.join();
	if (bar.joinable())
		bar.join();

	std::cout << "Joinable after construction:\n" << std::boolalpha;
	std::cout << "foo: " << foo.joinable() << '\n';
	std::cout << "bar: " << bar.joinable() << '\n';

	return 0;
}
/*
output:
mythread
Joinable after construction:
foo: false
bar: true
Joinable after construction:
foo: false
bar: false
*/
```


