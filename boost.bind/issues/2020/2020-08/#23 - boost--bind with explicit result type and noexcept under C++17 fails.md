# #23 - boost::bind with explicit result type and noexcept under C++17 fails [Closed]

> Username: Dani-Hub  
> Created at: 2020-08-27 16:39:17 UTC  
> Updated at: 2020-08-28 09:44:51 UTC  
> Closed at: 2020-08-28 09:44:51 UTC  
> Url: https://github.com/boostorg/bind/issues/23  

When compiling the following code requiring C++17 (or higher) with any recent compiler that has made exception specification part of the function type (Clang 12, gcc 11, VC 2019),   
```  
#include <boost/type.hpp>   
#include <boost/bind/bind.hpp>  
  
struct P  
{  
  P()   
  {  
    boost::bind(boost::type<void>(), &P::launch_me, this)();  
  }    
      
  void launch_me() noexcept {}  
};  
```  
  
the build fails, e.g. as follows:  
```  
In file included from prog.cc:2:  
In file included from /opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/bind.hpp:26:  
In file included from /opt/wandbox/boost-1.73.0/clang-head/include/boost/mem_fn.hpp:22:  
/opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/mem_fn.hpp:356:16: error: reference to non-static member function must be called; did you mean to call it with no arguments?  
        return (p->*f_);  
               ^~~~~~~~  
                       ()  
/opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/bind.hpp:259:9: note: in instantiation of member function 'boost::_mfi::dm<void () noexcept, P>::operator()' requested here  
        unwrapper<F>::unwrap(f, 0)(a[base_type::a1_]);  
        ^  
/opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/bind.hpp:1294:16: note: in instantiation of function template specialization 'boost::_bi::list1<boost::_bi::value<P *>>::operator()<void (P::*)() noexcept, boost::_bi::list0>' requested here  
        return l_( type<result_type>(), f_, a, 0 );  
               ^  
prog.cc:8:58: note: in instantiation of member function 'boost::_bi::bind_t<void, void (P::*)() noexcept, boost::_bi::list1<boost::_bi::value<P *>>>::operator()' requested here  
    boost::bind(boost::type<void>(), &P::launch_me, this)();  
                                                         ^  
In file included from prog.cc:2:  
In file included from /opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/bind.hpp:26:  
In file included from /opt/wandbox/boost-1.73.0/clang-head/include/boost/mem_fn.hpp:22:  
/opt/wandbox/boost-1.73.0/clang-head/include/boost/bind/mem_fn.hpp:356:16: error: non-const lvalue reference to type 'void () noexcept' cannot bind to a temporary of type 'void'  
        return (p->*f_);  
               ^~~~~~~~~  
2 errors generated.  
```  
The problem has been verified to exist at least for Boost 1.73 and the most recent version 1.74 as well.  
  
The problem was found by a more indirect usage of boost::bind via boost::thread in our code base using this kind of code:  
```  
#include <boost/thread.hpp>  
  
struct S  
{  
  S()   
  {  
    boost::thread th(&S::launch_me, this);  
  }    
      
  void launch_me() noexcept {}  
};  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2020-08-27 17:20:48 UTC  
> Url: https://github.com/boostorg/bind/issues/23#issuecomment-682083670  

Thanks, will fix shortly.

---

## Comment 2

> Username: Dani-Hub  
> Created at: 2020-08-27 17:40:14 UTC  
> Url: https://github.com/boostorg/bind/issues/23#issuecomment-682093255  

What a quick reaction - Thanks Peter!
