# #81 - noinitadaptor::destroy causes compiler error [Closed]

> Username: SquaredPotato  
> Created at: 2020-08-21 14:41:59 UTC  
> Updated at: 2021-03-25 10:36:48 UTC  
> Closed at: 2020-08-21 15:03:36 UTC  
> Url: https://github.com/boostorg/core/issues/81  

Error states  
`/opt/boost/1_74_0/include/boost/core/noinit_adaptor.hpp:58:10: error: expected a class name after '~' to name a destructor  p->~U();`  
  
Compiling on MacOS with Clang 10.0.0., with the -std=c++20 flag.  
I'm not sure why, but the error disappear when I replace the template class "U", with any other letter like "T", so that it looks like this:  
```  
    template<class T>  
    void samefunction(T* p) {  
        p->~T();  
    }  
```  
instead of  
```  
    template<class U>  
    void samefunction(U* p) {  
         p->~U();  
    }  
```  
Is this issue specific to my system? I can't seem to find anyone else having this problem.  
  
EDIT:  
Fixed by placing  
`#define _TURN_OFF_PLATFORM_STRING` at the top of my main.c file.  
Referenced in [cpprestsdk issue 1214](https://github.com/microsoft/cpprestsdk/issues/1214)

---

## Comment 1

> Username: Lastique  
> Created at: 2020-08-21 14:44:27 UTC  
> Url: https://github.com/boostorg/core/issues/81#issuecomment-678328005  

Check if you have a macro `U` defined somewhere.

---

## Comment 2

> Username: SquaredPotato  
> Created at: 2020-08-21 15:03:36 UTC  
> Updated at: 2020-08-21 15:04:14 UTC  
> Url: https://github.com/boostorg/core/issues/81#issuecomment-678339102  

Thanks for the quick response, it turns out it is indeed a macro defined by [cpprestsdk](https://github.com/Microsoft/cpprestsdk/) in basic_types.h.  
I guess this issue can be closed as it isn't a boost issue.

---

## Comment 3

> Username: nperdijk  
> Created at: 2021-03-25 10:36:48 UTC  
> Url: https://github.com/boostorg/core/issues/81#issuecomment-806541466  

I ran in the same issue as you. Thanks for your edit. That saved my day!
