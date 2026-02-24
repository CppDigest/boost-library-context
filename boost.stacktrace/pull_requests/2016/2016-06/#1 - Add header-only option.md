# #1 Add header-only option [Merged]

> Username: faithandbrave  
> Created at: 2016-06-23 05:41:51 UTC  
> Updated at: 2016-06-27 18:24:02 UTC  
> Merged at: 2016-06-27 18:23:53 UTC  
> Closed at: 2016-06-27 18:23:53 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/1  

similar feature to Boost.Chrono `BOOST_CHRONO_HEADER_ONLY`.  
  
``` cpp  
#define BOOST_STACKTRACE_HEADER_ONLY  
#define BOOST_STACKTRACE_USE_BACKTRACE  
#include <iostream>  
#include <boost/stacktrace.hpp>  
  
int main()  
{  
    std::cout << boost::stacktrace::stacktrace();  
}  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-06-23 05:45:39 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/1#issuecomment-227955579  

[![Coverage Status](https://coveralls.io/builds/6717715/badge)](https://coveralls.io/builds/6717715)  
  
Coverage remained the same at 98.413% when pulling **ac460c19cc59f06b9d7e71a027badb26a4cd74e7 on faithandbrave:add_header_only_option** into **13d8ee423c532c89de6f40f2d77b8941e4570387 on apolukhin:master**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-06-27 18:24:02 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/1#issuecomment-228831578  

Thanks for helping on early stages of development!

---
