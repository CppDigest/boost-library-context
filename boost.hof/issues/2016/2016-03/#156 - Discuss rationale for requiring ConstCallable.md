# #156 - Discuss rationale for requiring ConstCallable [Closed]

> Username: pfultz2  
> Created at: 2016-03-23 17:31:30 UTC  
> Updated at: 2016-04-28 03:37:23 UTC  
> Closed at: 2016-04-28 03:37:23 UTC  
> Url: https://github.com/boostorg/hof/issues/156  



---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-26 17:09:07 UTC  
> Url: https://github.com/boostorg/hof/issues/156#issuecomment-201893649  

First, there is surprising behavior using mutable function objects, since objects are freely copied everywhere:  
  
``` cpp  
struct counter  
{  
    int i;  
    counter() : i(0)  
    {}  
  
    template<class... Ts>  
    int operator()(Ts&&...)  
    {  
        return i++;  
    }  
};  
  
  
counter c{};  
by(mutable_(c))(1,1);  
// Prints 0  
std::cout << c.i << std::endl;  
```  
  
Instead `std::ref` should be used:  
  
``` cpp  
counter c{};  
by(std::ref(c))(1,1);  
// Prints 2  
std::cout << c.i << std::endl;  
```  
  
Secondly, in C++11 function objects can't be `constexpr` and have mutable overloads, since `constexpr` implies `const` in C++11.
