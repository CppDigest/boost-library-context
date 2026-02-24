# #387 - asio fatal error: winapifamily.h: No such file or directory [Closed]

> Username: sergiyvelichkevych  
> Created at: 2022-01-28 14:58:49 UTC  
> Updated at: 2022-03-29 00:01:15 UTC  
> Closed at: 2022-03-29 00:01:15 UTC  
> Url: https://github.com/boostorg/asio/issues/387  

Building the project in Ubuntu 20 with g++11 with boost 1.78  
with   
  
#include <boost/asio/ssl.hpp>  
  
Got error:  
/vcpkg/installed/x64-linux/include/boost/asio/detail/config.hpp:1320:12: fatal error: winapifamily.h: No such file or directory  
  
It looks like broken defines for detecting OS

---

## Comment 1

> Username: ulatekh  
> Created at: 2022-03-28 23:07:23 UTC  
> Url: https://github.com/boostorg/asio/issues/387#issuecomment-1081237237  

You appear to be building the vcpkg version under Linux.  
Isn't the vcpkg variant specific to MS Windows?

---

## Comment 2

> Username: sergiyvelichkevych  
> Created at: 2022-03-29 00:01:15 UTC  
> Url: https://github.com/boostorg/asio/issues/387#issuecomment-1081264557  

It was my mistake. I defined specific windows value  in my code and Linux builds failed because of this.
