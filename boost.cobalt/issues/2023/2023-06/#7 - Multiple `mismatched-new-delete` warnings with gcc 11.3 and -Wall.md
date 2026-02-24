# #7 - Multiple `mismatched-new-delete` warnings with gcc 11.3 and -Wall [Open]

> Username: jsaf0  
> Created at: 2023-06-29 19:19:47 UTC  
> Updated at: 2025-04-23 11:37:13 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7  

When building latest master (`53fded2`) with gcc 11.3 (Ubuntu 22.04) and `-Wall`, the compiler complains about mismatched new-delete warnings. See snippet below:  
  
```  
/home/jfp/async/test/async_for.cpp: In function ‘boost::async::generator<int> test_data_gen()’:  
/home/jfp/async/test/async_for.cpp:30:1: warning: ‘static void boost::async::promise_memory_resource_base::operator delete(void*, std::size_t)’ called on pointer returned from a mismatched allocation function [-Wmismatched-new-delete]  
   30 | }  
```  
  
Is this warning safe to ignore?

---

## Comment 1

> Username: jsaf0  
> Created at: 2023-06-30 07:18:54 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7#issuecomment-1614236699  

FYI, gcc 13.0.1 does not give the same warnings.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-06-30 08:09:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7#issuecomment-1614291953  

From what I remember yes, but you might want to run the tests with the address sanitizer before using it in prod.  
  
If this is an error (be it in the compiler), I can just remove the alloc-optimizations on that version.

---

## Comment 3

> Username: jsaf0  
> Created at: 2023-06-30 10:31:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7#issuecomment-1614462691  

No errors from building and running the unit tests with address sanitizer enabled - both on gcc 11.3.0 and gcc 13.0.1. So assume it's safe to ignore for now.  
  
My previous comment from before that gcc 13.0.1 doesn't complain about mismatched-new-delete was not correct. When building in "debug mode" with CMake, it still spits out all these warnings. So it would definitely be nice to find a fix in order to avoid disabling this warning for all code.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-24 08:53:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7#issuecomment-1691280524  

I am pinning this because it'll come up again. This is safe, the gcc warnign is a false positive. It machtes the new & delete function signatures, and they don't match for coroutine allocations.

---

## Comment 5

> Username: MarekNiemiec  
> Created at: 2025-04-22 13:43:59 UTC  
> Updated at: 2025-04-23 11:37:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/7#issuecomment-2821383820  

I was able to reproduce -Wmismatched-new-delete via something like this:  
`awaitable<std::tuple<_,_,Config>>`  
Where Config is  
```c++  
struct Config{  
...  
std::array<Param,10>  
...  
}  
````  
Where Param is another struct(and another quite complex with some strings and another 2 structs inside.  
Problem is only reproducible on our ci/cd  
Commenting line with `std::array<Param,10> solves issue.  
If I add Param param[10] issue.  
If I change and add 10 separate Param structs no issue.  
If I do std::array<int,10> no issue.  
  
I can summarize it with problem appears if there is array/std::array with struct inside.  
Struct itself is not a problem as I can do 10 structs inside(instead of array 10 elements).  
  
We have gcc 14.2.  
boost.asio 1.87  
Visible only with -O2 -DNDEBUG  
  
Just to remmind error:  
```  
[with Executor = boost::asio::any_io_executor]' called on pointer returned from a mismatched allocation function [-Werror=mismatched-new-delete]  
<some not important data here>  
/include/boost/asio/detail/memory.hpp:79:33: note: returned from 'void* aligned_alloc(size_t, size_t)'  
   79 |   void* ptr = std::aligned_alloc(align, size);  
```
