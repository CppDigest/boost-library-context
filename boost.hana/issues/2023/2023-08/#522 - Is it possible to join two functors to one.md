# #522 - Is it possible to join two functors to one? [Open]

> Username: denzor200  
> Created at: 2023-08-24 23:15:42 UTC  
> Updated at: 2023-08-25 04:11:12 UTC  
> Url: https://github.com/boostorg/hana/issues/522  

I'm finding something like this:  
```  
struct functor_1 {  
    void operator() (std::string arg1, std::string arg2)  
    {  
        std::cout << arg1 << arg2 " from first functor" << std::endl  
    }  
};  
struct functor_2 {  
    void operator() (std::string arg1, std::string arg2)  
    {  
        std::cout << arg1 << arg2 " from second functor" << std::endl  
    }  
};  
join(functor_1(), functor_2())("Hello, ", "World!"); // will print two lines:  
// "Hello, World! from first functor"  
// .. and  
// "Hello, World! from second functor"  
```  
Is it possible to do using boost.hana?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2023-08-25 04:11:11 UTC  
> Url: https://github.com/boostorg/hana/issues/522#issuecomment-1692728204  

Yes, I would lean towards using lambdas, but sometimes capturing and forwarding arguments gets messy. Note that I made the inner lambda `mutable` to support non-const "functors" as you called them.  
```cpp  
#include <boost/hana.hpp>  
#include <iostream>  
  
namespace hana = boost::hana;  
  
struct functor_1 {  
    void operator() (std::string arg1, std::string arg2)  
    {  
        std::cout << arg1 << arg2 << " from first functor" << '\n';  
    }  
};  
struct functor_2 {  
    void operator() (std::string arg1, std::string arg2)  
    {  
        std::cout << arg1 << arg2 << " from second functor" << '\n';  
    }  
};  
auto join = [](auto&& ...fn) {  
    auto fns = hana::make_tuple(static_cast<decltype(fn)>(fn)...);  
    return [fns = std::move(fns)](auto&& ...args) mutable {  
        return hana::for_each(  
            fns, hana::reverse_partial(hana::apply, static_cast<decltype(args)>(args)...));  
    };  
#if 0 // Never go full demux.  
    return hana::demux(hana::compose(hana::reverse_partial(hana::for_each, hana::apply),   
                                     hana::partial(hana::transform, std::move(fns))))  
                                        (hana::capture);  
#endif  
};  
  
int main() {  
    join(functor_1(), functor_2())("Hello, ", "World!"); // will print two lines:  
}  
```  
https://godbolt.org/z/azoYToM6a
