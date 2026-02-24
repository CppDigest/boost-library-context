# #212 Assertion in scheduler.cpp with work_stealing algo and timed-out wait ops [Closed]

> Username: saschazelzer  
> Created at: 2019-08-29 07:37:54 UTC  
> Updated at: 2021-03-01 06:54:27 UTC  
> Closed at: 2021-03-01 06:54:27 UTC  
> Url: https://github.com/boostorg/fiber/pull/212  

I run into crashes in my application when using worker threads with the work_stealing scheduling algorithm and waits timing out on a buffered_channel.  
  
It seems to be closely related to issue #166, but for a work_stealing scheduling algorithm. If requested, I could try and extract a minimal working example to reproduce this.

---

## Comment 1

> Username: olk  
> Created_at: 2019-08-29 08:44:07 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-526088551  

please provide a minimal example - a fiber-context must not detached/attached from/to a scheduler while waiting for some event (stored in schedulers wait-list)

---

## Comment 2

> Username: saschazelzer  
> Created_at: 2019-08-29 16:51:01 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-526270838  

All right, I will. To make sure there is no misunderstanding, the assertion is triggered when the wait operation timed out and the fiber is ready to be resumed.

---

## Comment 3

> Username: saschazelzer  
> Created_at: 2019-09-04 14:30:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-527927170  

The following gist contains a self-contained example triggering the assertion:  
  
```cpp  
#include <iostream>  
#include <boost/fiber/all.hpp>  
  
int main() {  
  
	boost::fibers::barrier barrier{ 2 };  
  
	boost::fibers::mutex m;  
	boost::fibers::condition_variable cv;  
  
	boost::fibers::buffered_channel<int> channel{ 2 };  
  
	int count = 2;  
  
	std::thread([&] {  
		boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(2);  
		barrier.wait();  
  
		std::unique_lock<boost::fibers::mutex> l(m);  
		cv.wait(l, [&] { return count == 0; });  
	}).detach();  
  
	boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(2);  
	barrier.wait();  
  
	boost::fibers::fiber([&] {  
		for (;;) {  
			int x;  
			if (channel.pop_wait_for(x, std::chrono::milliseconds(100)) ==  
				boost::fibers::channel_op_status::timeout) {  
				std::cout << "timed out" << std::endl;  
			}  
			else {  
				std::cout << "got value: " << x << std::endl;  
			}  
		}  
		count--;  
	}).detach();  
  
	boost::fibers::fiber([&] {  
		for (int x = 0;; x++) {  
			channel.push(x);  
			boost::this_fiber::sleep_for(std::chrono::seconds(1));  
		}  
		count--;  
	}).detach();  
  
	std::unique_lock<boost::fibers::mutex> l(m);  
	cv.wait(l, [&] { return count == 0; });  
  
	return 0;  
}  
```  
  
Tested on Windows 10, using a static 64-bit debug version of Boost 1.70. The program crashes with  
  
    Assertion failed: ! ctx->wait_is_linked(), file ..\src\scheduler.cpp, line 405

---

## Comment 4

> Username: olk  
> Created_at: 2019-09-11 04:48:43 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-530218011  

I don't use Windows as dev platform - on Linux the code works as expected.

---

## Comment 5

> Username: saschazelzer  
> Created_at: 2019-09-12 08:55:06 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-530732142  

That is weird. I just tested the same code on a Debian 10 VM with custom built debug boost libraries. I run into the same assertion, aborting the program.

---

## Comment 6

> Username: olk  
> Created_at: 2019-09-12 10:50:23 UTC  
> Updated_at: 2019-09-12 10:50:52 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-530771760  

I'm using branch develop - executes your test app in a loop

---

## Comment 7

> Username: saschazelzer  
> Created_at: 2019-09-12 17:36:01 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-530928464  

The Debian VM is using boost-fiber 1.71 and the only difference to the develop branch seems to be the addition of the destructor call for values in the buffered_channel class template. So I could try again with develop, but I don't see why that would make a difference.  
  
I can reliably reproduce this on two different platforms now. Any idea what I could do in addition to help diagnose this?  
  
Just to be sure to rule out some basic issues,  are you sure your test app linked against a debug build of boost-fiber which does not discard the asserts?

---

## Comment 8

> Username: gpascualg  
> Created_at: 2020-06-03 03:52:55 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-637936540  

I confirm being able to reproduce this with boost 1.73 on MSVC Version 16.7.0 Preview 1.0, building with /std:c++latest and /permissive- and linking against a debug library.

---

## Comment 9

> Username: olk  
> Created_at: 2021-03-01 06:54:26 UTC  
> Url: https://github.com/boostorg/fiber/pull/212#issuecomment-787700490  

should be fixed by #261

---
