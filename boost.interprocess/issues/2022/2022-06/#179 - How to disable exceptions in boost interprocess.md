# #179 - How to disable exceptions in boost interprocess? [Closed]

> Username: prncoprs  
> Created at: 2022-06-26 18:07:37 UTC  
> Updated at: 2022-07-16 20:41:49 UTC  
> Closed at: 2022-07-16 20:41:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/179  

I want to disable the exceptions in my program.  
  
I defined `BOOST_EXCEPTION_DISABLE` and `BOOST_NO_EXCEPTIONS`. Then I included the boost::interprocess related head files in my program. And I use `gcc -o example example1.cpp -fno-exceptions` to compile my program.   
  
However, the compilation logs said just as follows.  
  
```  
In file included from /usr/include/boost/interprocess/detail/managed_memory_impl.hpp:29,  
                 from /usr/include/boost/interprocess/managed_shared_memory.hpp:25,  
                 from example1.cpp:5:  
/usr/include/boost/interprocess/exceptions.hpp: In constructor ‘boost::interprocess::interprocess_exception::interprocess_exception(const char*)’:  
/usr/include/boost/interprocess/exceptions.hpp:43:14: error: exception handling disabled, use ‘-fexceptions’ to enable  
   43 |       catch (...) {}  
      |              ^~~  
```  
  
So, does this mean that I had failed to disabled the exceptions in my program?  
Can someone help me with it, so that I can use `boost::interprocess` and `-fno-exceptions` together?  
  
Thanks very much!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 20:41:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/179#issuecomment-1186287505  

I'm sorry to tell you that Boost.Interprocess does not support builds with disabled exceptions. Supporting exception-less builds would requiere a redesign of many interfaces because many functions and classes...
