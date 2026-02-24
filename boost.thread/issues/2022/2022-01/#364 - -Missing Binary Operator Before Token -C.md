# #364 - "Missing Binary Operator Before Token "C" " [Open]

> Username: hilga007  
> Created at: 2022-01-19 06:33:02 UTC  
> Updated at: 2023-07-19 12:19:14 UTC  
> Url: https://github.com/boostorg/thread/issues/364  

Another user on Ubuntu 20.04 who is experiencing this issue with gcc 10.x, I'm on Ubuntu 22.04 with gcc 11.2.0. (Quoting the original issue which is from 2019 and has been closed; where someone had an issue on Solaris)  
  
My specific issue (Identical to original)   
  
```  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("  
   60 | #if PTHREAD_STACK_MIN > 0  
      |     ^~~~~~~~~~~~~~~~~  
  
```  
  
  
  
  
Still facing this issue. Is the issue closed? Ubuntu 20.04 and g++ --version = 10.3.0  
  
_Originally posted by @seerviashish in https://github.com/boostorg/thread/issues/283#issuecomment-1003548201_  
  
_Related to Issue in KAPOWMINER: https://github.com/RavenCommunity/kawpowminer/issues/111_

---

## Comment 1

> Username: JcBernack  
> Created at: 2022-03-04 12:49:52 UTC  
> Updated at: 2022-03-04 12:52:32 UTC  
> Url: https://github.com/boostorg/thread/issues/364#issuecomment-1059134759  

I have the same issue on Ubuntu 21.10 with Boost 1.70.0.  
Tried with GCC 11.2.0, 9.4.0 and 8.5.0, error stays the same.  
  
I modified `thread_data.hpp` to echo the value of `PTHREAD_STACK_MIN` and it is `__sysconf (75)`.  
If I add the following to that file it builds without a problem:  
```  
#undef PTHREAD_STACK_MIN  
#define PTHREAD_STACK_MIN 16384  
```  
I have yet to try a newer version of Boost if anything was changed here.

---

## Comment 2

> Username: EchterAgo  
> Created at: 2022-04-01 10:21:55 UTC  
> Url: https://github.com/boostorg/thread/issues/364#issuecomment-1085723092  

This is fixed by https://github.com/boostorg/thread/pull/297

---

## Comment 3

> Username: esamet  
> Created at: 2023-07-19 12:19:13 UTC  
> Url: https://github.com/boostorg/thread/issues/364#issuecomment-1641981518  

> I have the same issue on Ubuntu 21.10 with Boost 1.70.0. Tried with GCC 11.2.0, 9.4.0 and 8.5.0, error stays the same.  
>   
> I modified `thread_data.hpp` to echo the value of `PTHREAD_STACK_MIN` and it is `__sysconf (75)`. If I add the following to that file it builds without a problem:  
>   
> ```  
> #undef PTHREAD_STACK_MIN  
> #define PTHREAD_STACK_MIN 16384  
> ```  
>   
> I have yet to try a newer version of Boost if anything was changed here.  
  
yes my problem also fixed with paste that command top of the if
