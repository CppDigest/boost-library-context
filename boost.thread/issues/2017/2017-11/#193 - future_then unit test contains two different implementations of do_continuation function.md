# #193 - future_then unit test contains two different implementations of do_continuation function [Closed]

> Username: westion717  
> Created at: 2017-11-23 03:57:45 UTC  
> Updated at: 2018-02-20 17:11:59 UTC  
> Closed at: 2018-02-20 17:11:59 UTC  
> Url: https://github.com/boostorg/thread/issues/193  

`clang++ -c -x c++ -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -O3 -Wno-inline -Wall -pthread -fPIC -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_THROW_IF_PRECONDITION_NOT_SATISFIED -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -I".."  "../libs/thread/test/../example/future_then.cpp" -E > future_then.i`  
  
```c++  
void do_continuation(boost::unique_lock<boost::mutex>& lock)  
{  
  if (! continuations.empty()) {  
    continuations_type the_continuations = continuations;  
    continuations.clear();  
    relocker rlk(lock);  
    for (continuations_type::iterator it = the_continuations.begin(); it != the_continuations.end(); ++it) {  
      (*it)->launch_continuation();  
    }  
  }  
}  
```  
`clang++ -c -x c++ -Wextra -Wno-long-long -Wno-unused-parameter -Wno-variadic-macros -Wunused-function -O0 -g -fno-inline -Wall -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wno-variadic-macros -Wunused-function -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -I".."  "../libs/thread/src/pthread/thread.cpp" -E  > thread.i`  
  
```c++  
void do_continuation(boost::unique_lock<boost::mutex>& lock)  
{  
}  
```  
The essential reason is that the macro `#define BOOST_THREAD_VERSION 4` defined in future_then.cpp makes the implementation of do_continuation in future_then.o different from that in thread.o.

---

## Comment 1

> Username: viboes  
> Created at: 2017-11-23 18:01:35 UTC  
> Updated at: 2018-02-03 08:30:06 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-346676279  

These functions are inlined and not used in thread.cpp, isn't it?  
So this shouldn't be a real problem. Have you found some execution issue?  
  
If there is a real problem, and only then  
  
* one possible fix will consists in moving these functions from `shared_state_base` to any class templated that inherits form `shared_state_base`.  
  
* the other is just say that any revision before version 4 is not supported anymore.  
  
Any PR would be welcome in this case.

---

## Comment 2

> Username: westion717  
> Created at: 2017-11-24 06:19:03 UTC  
> Updated at: 2017-11-24 06:20:11 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-346751111  

The compiled option is `-fno-inline`. Even the function `do_continuation` is inlined, the parent inlining the function will also have the same issue.  
  
As the following command shows, the symbol is weak and the linker will only choose one definition. Since the linker just chooses the one from future_then.o, so the execution issue is veiled. But it's not stable. When use the high level optimization between object files provided by compiler such as LTO of clang or IPA of XL, it will be more possible to choose the bad one.  
  
  
`nm thread.o |grep do_conti`  
0000000000000000 **W** _ZN5boost6detail17shared_state_base15do_continuationERNS_11unique_lockINS_5mutexEEE  
`nm future_then.o |grep do_conti`  
0000000000000000 **W** _ZN5boost6detail17shared_state_base15do_continuationERNS_11unique_lockINS_5mutexEEE  
  
  
I think every definition of one per function coming from different object files should be consistent.  
I am not experienced with the boost development, I hope somebody can make a PR to fix this issue.

---

## Comment 3

> Username: viboes  
> Created at: 2017-11-24 07:26:54 UTC  
> Updated at: 2018-02-03 08:31:12 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-346759863  

> I think every definition of one per function coming from different object files should be consistent.  
  
Me too, and this one escaped me. This makes maybe the program ill formed due to ODR.   
  
This go to the stack of things to improve.  
  
Thanks for catching it.

---

## Comment 4

> Username: viboes  
> Created at: 2017-11-24 07:29:22 UTC  
> Updated at: 2017-11-24 07:31:51 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-346760182  

> As the following command shows, the symbol is weak and the linker will only choose one definition. Since the linker just chooses the one from future_then.o, so the execution issue is veiled. But it's not stable. When use the high level optimization between object files provided by compiler such as LTO of clang or IPA of XL, it will be more possible to choose the bad one.  
  
Then yes, we have a clear ODR, and a fix could be more urgent. Have you  have other cases?  
  
Another solution is to provide a different binary for each thread version. I would see what can be done in this sense.

---

## Comment 5

> Username: westion717  
> Created at: 2017-11-27 02:09:20 UTC  
> Updated at: 2017-11-27 02:10:24 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-347060824  

Currently, I do not find other cases. But it's better to have a check whether there are others cases which also have this issue.   
I would pull the patch to fix my local source once the patch is OK. Thanks.

---

## Comment 6

> Username: viboes  
> Created at: 2018-02-03 09:09:10 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-362792357  

I'll try to move the definition the the derived templates. This implies that `do_continuation` must virtual :(   
  
When I will remove the support of no-continuation (maybe never), I could rollback this.

---

## Comment 7

> Username: viboes  
> Created at: 2018-02-20 17:11:59 UTC  
> Url: https://github.com/boostorg/thread/issues/193#issuecomment-367048508  

https://github.com/boostorg/thread/commit/473648ce6c6ea6325e7e8824cafc37993339d5cd
