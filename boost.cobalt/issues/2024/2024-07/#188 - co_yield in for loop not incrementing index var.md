# #188 - co_yield in for loop not incrementing index var. [Closed]

> Username: kilasuelika  
> Created at: 2024-07-05 15:07:38 UTC  
> Updated at: 2024-07-22 02:44:31 UTC  
> Closed at: 2024-07-07 02:42:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/188  

Following code:  
```  
#include <boost/cobalt.hpp>  
#include <iostream>  
#include <optional>  
  
using namespace boost;  
  
cobalt::generator<std::optional<int>> next(int k) {  
    for (int i = 0; i < k; ++i) {  
        std::println(std::cout, "getting {}", i);  
        co_yield i;  
    }  
    co_return{};  
}  
  
cobalt::main co_main(int argc, char* argv[]) {  
    while (auto x = co_await next(5)) {  
        std::println(std::cout, "got {}", x.value());  
    }  
    co_return 0;  
}  
```  
In my MSVC, the console keeps showing "getting 0" and never finished. Seems the `i` is not incremented. Is this a desired behavior?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-07-07 02:42:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/188#issuecomment-2212296765  

Yes, because you are creating a new generator when you call `next(5)` and then destroying it rightaway.  
  
```cpp  
cobalt::main co_main(int argc, char* argv[]) {  
    auto g = next(5);  
    while (auto x = co_await  g) {  
        std::println(std::cout, "got {}", x.value());  
    }  
    co_return 0;  
}  
```  
  
That should do it.

---

## Comment 2

> Username: kilasuelika  
> Created at: 2024-07-22 02:44:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/188#issuecomment-2241938917  

> Yes, because you are creating a new generator when you call `next(5)` and then destroying it rightaway.  
>   
> ```c++  
> cobalt::main co_main(int argc, char* argv[]) {  
>     auto g = next(5);  
>     while (auto x = co_await  g) {  
>         std::println(std::cout, "got {}", x.value());  
>     }  
>     co_return 0;  
> }  
> ```  
>   
> That should do it.  
  
Oh thank you, I forgot that. I'm also wondering will the range-for be supported to use `for (const auto ele: next(5))`.
