# #44 - boost::rational issue with ios_base constructor [Open]

> Username: john-d-murphy  
> Created at: 2020-07-18 18:23:14 UTC  
> Updated at: 2020-07-18 18:47:17 UTC  
> Url: https://github.com/boostorg/rational/issues/44  

Code fails to compile if boost::rational is used as the parameter to a function that also shares a signature with a void* - for example:  
  
```  
#include <boost/rational.hpp>  
  
void foo(void(*)()) {}  
void foo(boost::rational<int>) {}  
  
void fun() {}  
  
int main() {  
  foo(fun);  
}  
```  
  
https://wandbox.org/permlink/a06FvkYIP5asIys0  
  
This returns an error in  from /opt/wandbox/gcc-head/include/c++/11.0.0/bits/ios_base.h:41,  
  
Apologies since I'm not familiar with the code enough to give a better example/understanding of what's happening. Please let me know what other information I can provide or how I can help get this sorted.  
  
thanks,  
John

---

## Comment 1

> Username: mclow  
> Created at: 2020-07-18 18:41:47 UTC  
> Url: https://github.com/boostorg/rational/issues/44#issuecomment-660524011  

It looks to me like it trying to instantiate `std::numeric_limits<void ()>` which doesn't go well.  
  
```  
/opt/wandbox/gcc-head/include/c++/11.0.0/limits: In instantiation of 'struct std::numeric_limits<void()>':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/rational.hpp:123:7:   required from 'const bool boost::rational_detail::is_compatible_integer<void(), int, void>::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/rational.hpp:184:21:   required by substitution of 'template<class T> constexpr boost::rational<int>::rational(const T&, const typename boost::enable_if_c<boost::rational_detail::is_compatible_integer<T, int, void>::value, void>::type*) [with T = void()]'  
prog.cc:9:10:   required from here  
```

---

## Comment 2

> Username: mclow  
> Created at: 2020-07-18 18:47:17 UTC  
> Url: https://github.com/boostorg/rational/issues/44#issuecomment-660524659  

Also, I get the same error from clang/libc++
