# #36 - boost format operator % won't accept dereference of volatile [Closed]

> Username: jeking3  
> Created at: 2017-10-18 19:21:15 UTC  
> Updated at: 2017-12-18 15:36:38 UTC  
> Closed at: 2017-10-26 00:02:28 UTC  
> Url: https://github.com/boostorg/format/issues/36  

Originally reported by [Paul Rose in Boost Trac](https://svn.boost.org/trac10/ticket/10852).  
  
```  
struct A{  
    A() : a(9) {} int a;  
}; A a; A volatile *pva = &a;  
  
std::cout << boost::format("%d") % pva->a << std::endl;  
  
This worked in 1_54 but fails in 1_56 with error:  
    format/feed_args.hpp:135:47: error: invalid conversion from 'volatile void*' to 'const void*'  
  
Can work around this with cast:  
    std::cout << boost::format("%d") % (int)pva->a << std::endl;  
but should not have to. The appearance to the user is pass by value.  
  
Running linux with g++ 4.6.2 and 4.8.2  
```  
  
Originally reported in Boost 1.56.0.  
Confirmed in Boost 1.59.0 with clang-3.8 on linux.  
Confirmed in Boost 1.61.0 with VS2008.  
Confirmed in Boost 1.63.0 with gcc-5.2.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-25 22:31:36 UTC  
> Url: https://github.com/boostorg/format/issues/36#issuecomment-339494009  

My solution in the pull request is to make a copy of the argument being passed in; by making a copy of it we force it to be read, and the copy is not volatile, then we pass the copy on through format's normal machinery.
