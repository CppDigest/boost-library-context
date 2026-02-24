# #27 - C++11 example for asio spawn [Closed]

> Username: absolute8511  
> Created at: 2015-01-12 07:07:19 UTC  
> Updated at: 2015-02-26 12:51:02 UTC  
> Closed at: 2015-02-26 12:51:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/27  

I tried to use the boost::fibers::asio::spawn while using c++11, but it failed to compile and I noticed the example is missing under cpp11 directory.  Is there any example for the usage? Or is it not supported under c++11?

---

## Comment 1

> Username: erakadjiev  
> Created at: 2015-02-21 12:54:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/27#issuecomment-75370405  

Asio in the `master` branch works currently only with C++03. With relatively trivial modifications I could compile it with C++11, but it still failed during runtime.   
You can use the `develop` branch and C++14. Related details are in #29.

---

## Comment 2

> Username: olk  
> Created at: 2015-02-26 12:51:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/27#issuecomment-76172741  

please note that this project is still in development
