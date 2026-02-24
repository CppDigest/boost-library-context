# #98 - has_ready_fibers() always returns true [Closed]

> Username: xaqq  
> Created at: 2016-11-17 15:02:39 UTC  
> Updated at: 2016-11-18 07:42:58 UTC  
> Closed at: 2016-11-18 07:42:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/98  

Hello,  
  
I've been playing with boost::fibers and I've found something strange that might be a bug. It might also be me misusing / misunderstand how things work.  
Initially, my goal was to integrate asio and fibers, so I've ready the tutorial and used the `fibers::asio::round_robin` algorithm.  
  
Consider the following simple program.  
  
```  
int main()  
{  
   // assert(!boost::fibers::has_ready_fibers()); Assertion would fail. Weird?  
    boost::asio::io_service io_service_;  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_service_);  
    io_service_.run();  
    return 0;  
}  
```  
  
This causes the program to run at 100% cpu usage. I've tried to debug a bit, but without much success. It seems that `has_ready_fibers()` always returns true, causing the CPU to spin.  
As you can see, even without setting any scheduling algorithm, `has_ready_fibers()` returns true when there should be no such ready fibers.  
  
----  
  
I've tried to show the issue with an even simpler program. So I've added debug print in the library and recompiled.  
  
This is the modified `has_ready_fibers()` for `boost::fibers::algo::round_robin` (the default algorithm)  
```  
// The modified has_ready_fibers() for debug output  
bool  
round_robin::has_ready_fibers() const noexcept {  
	std::cout << "In has_ready_fibers(), will list ready fibers. Current fiber id: "  
            << context::active()->get_id() << std::endl;  
  for (const auto &f : rqueue_)  
    {  
      std::cout << "\tFiber: " << f.get_id() << std::endl;  
    }  
  return ! rqueue_.empty();  
}  
```  
  
  
And this is the corresponding program that shows the issue.  
```  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/fiber/all.hpp>  
  
int main()  
{  
  std::cout << "My fiber id: " << boost::this_fiber::get_id() << std::endl;  
  boost::fibers::has_ready_fibers();  
}  
  
/*  
 The follwing is outputed by the program.  
My fiber id: 0x1d5f270  
In has_ready_fibers(), will list ready fibers. Current fiber id: 0x1d5f270  
        Fiber: 0x1d6f3a8  
*/  
```  
  
It seems there is a fiber that comes from I don't know where, and seems to cause some  
issue.   
  
Now it's quite possible that I'm missing something, so please let me know if that's the case.  
Thanks,

---

## Comment 1

> Username: olk  
> Created at: 2016-11-17 17:43:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/98#issuecomment-261316111  

on each thread you have at least two fibers - one represents the thread itself (main.fiber) + a dispatcher fiber for maintenance purposes.  
boost.asio integration is not finished and needs support from the asio authors. unfortunately I haven't got a response yet (reason that the code is in examples directory).

---

## Comment 2

> Username: olk  
> Created at: 2016-11-18 07:42:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/98#issuecomment-261468498  

due the work to get the asio example fibers shared between threads, which failed because of some missing guarantees of asios's io_service. the code remained it this state - commit de58b076ebb19f881f3aa8401a9689b6ef481680 (branch develop) contains a recovered version of asio-sched-algorithm
