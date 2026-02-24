# #369 Fix issue #366 (adding testcase) [Closed]

> Username: sehe  
> Created at: 2022-05-04 00:39:57 UTC  
> Updated at: 2022-05-14 00:44:31 UTC  
> Closed at: 2022-05-13 22:53:43 UTC  
> Url: https://github.com/boostorg/thread/pull/369  

With reference to https://github.com/boostorg/thread/issues/366 and  
https://github.com/boostorg/thread/pull/367  
  
This fix is narrower than the linked PR in that it only prevents interruption exceptions from being raised inside the thread_guard destructor.

---

## Comment 1

> Username: sehe  
> Created_at: 2022-05-10 16:01:14 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122590610  

Need to also have a look at scope_guard and scoped_thread (thanks for the hint @elsamuko)

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-05-10 16:46:25 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122636169  

As far as I can see the minimal test just needs to be  
```  
#include "boost/thread/thread_guard.hpp"  
#include "boost/thread.hpp"  
#include <iostream>  
  
static void inner_thread_func()  
{  
    boost::this_thread::sleep_for(  
        boost::chrono::hours(1) );  
}  
  
static void outer_thread_func()  
{  
    boost::thread inner( inner_thread_func );  
    boost::thread_guard<boost::interrupt_and_join_if_joinable> guard( inner );  
}  
  
static void double_interrupt()  
{  
    boost::thread outer( outer_thread_func );  
    outer.interrupt();  
    outer.join();  
}  
  
int main()  
{  
    std::cout << "Start" << std::endl;  
    double_interrupt();  
    std::cout << "End" << std::endl;  
}  
```  
and, correspondingly,  
```  
#include "boost/thread/scoped_thread.hpp"  
#include "boost/thread.hpp"  
#include <iostream>  
  
static void inner_thread_func()  
{  
    boost::this_thread::sleep_for(  
        boost::chrono::hours(1) );  
}  
  
static void outer_thread_func()  
{  
    boost::scoped_thread<boost::interrupt_and_join_if_joinable> inner( inner_thread_func );  
}  
  
static void double_interrupt()  
{  
    boost::scoped_thread<boost::interrupt_and_join_if_joinable> outer( outer_thread_func );  
}  
  
int main()  
{  
    std::cout << "Start" << std::endl;  
    double_interrupt();  
    std::cout << "End" << std::endl;  
}  
```  
I'm not sure we need to do anything about `scoped_guard`, although I suppose it won't hurt to disable interrupts in its destructor, too. There's no scenario in which you want them not disabled.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-05-10 16:48:16 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122638069  

Now that I look at it, maybe we can replace  
```  
static void double_interrupt()  
{  
    boost::thread outer( outer_thread_func );  
    outer.interrupt();  
    outer.join();  
}  
```  
with  
```  
static void double_interrupt()  
{  
    boost::thread outer( outer_thread_func );  
    boost::thread_guard<boost::interrupt_and_join_if_joinable> guard( outer );  
}  
```  
for symmetry.

---

## Comment 4

> Username: elsamuko  
> Created_at: 2022-05-10 16:53:36 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122642910  

@sehe @pdimov `scope_guard` was just a helper class in my example code and is not part of `boost::thread`. The thread functors are currently used in  
* `boost::strict_scoped_thread`  
* `boost::scoped_thread`  
* `boost::thread_guard`

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-05-10 16:54:42 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122643856  

Well, we can't do anything about user-defined scope guards.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-05-10 16:57:27 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122646365  

All right, we need a third test for `strict_scoped_thread`, then.

---

## Comment 7

> Username: sehe  
> Created_at: 2022-05-10 17:12:30 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1122659833  

I'll add the test and fix for `*scoped_thread`

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-05-13 22:53:43 UTC  
> Url: https://github.com/boostorg/thread/pull/369#issuecomment-1126562163  

Should be fixed with https://github.com/boostorg/thread/commit/f71e0f1645413c3291e37d766e5b04bc57c6e132 and https://github.com/boostorg/thread/commit/8db325363b8e91de23c062ec8964bb605ad89f11.

---
