# #354 Fix -Wsign-compare warning with glibc 2.34 on Linux platforms. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2021-08-10 14:24:46 UTC  
> Updated at: 2021-10-26 09:53:40 UTC  
> Merged at: 2021-10-26 04:44:57 UTC  
> Closed at: 2021-10-26 04:44:57 UTC  
> Url: https://github.com/boostorg/thread/pull/354  

```  
In file included from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/thread/thread_only.hpp:17,  
                 from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/thread/thread.hpp:12,  
                 from src/GetTest.cpp:12:  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/thread/pthread/thread_data.hpp: In member function 'void boost::thread_attributes::set_stack_size(std::size_t)':  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/thread/pthread/thread_data.hpp:61:19: error: comparison of integer expressions of different signedness: 'std::size_t' {aka 'long unsigned int'} and 'long int' [-Werror=sign-compare]  
   61 |           if (size<PTHREAD_STACK_MIN) size=PTHREAD_STACK_MIN;  
      |                   ^  
```

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-10-25 22:09:03 UTC  
> Url: https://github.com/boostorg/thread/pull/354#issuecomment-951370822  

@pdimov Could we merge this please, so that it makes it into 1.78? These warnings are annoying.

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-10-26 09:53:40 UTC  
> Url: https://github.com/boostorg/thread/pull/354#issuecomment-951773493  

Thanks.

---
