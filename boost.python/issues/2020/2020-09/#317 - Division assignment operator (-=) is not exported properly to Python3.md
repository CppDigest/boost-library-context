# #317 - Division assignment operator (/=) is not exported properly to Python3 [Open]

> Username: acalloo  
> Created at: 2020-09-23 14:43:36 UTC  
> Updated at: 2020-12-07 09:12:32 UTC  
> Url: https://github.com/boostorg/python/issues/317  

The minimal example I have is for g++8.3.0 and boost1.67 on Ubuntu19.04 (although I have tested boost1.74 on a CentOS7.8 platform).   
  
My understanding of the issue is that when the operator "/=" is exported for Python3, it is binded to \_\_idiv__ and not \_\_itruediv__, therefore causing a crash upon runtime. For Python2, there is no problem observed.   
  
The problem solved by explicitly doing .def("\_\_itruediv__", &Foo::operator/=). Is this a desired behaviour?  
  
PS:  
Compiling on Ubuntu19.04 as:   
g++ foo.cpp -Wall -Wextra -shared -fPIC -o myModule.so -Wfatal-errors -I/usr/include/python3.7 -lboost_python3 (boost1.67 library and boost1.67-dev installed from packages).

---

## Comment 1

> Username: acalloo  
> Created at: 2020-09-23 14:45:38 UTC  
> Url: https://github.com/boostorg/python/issues/317#issuecomment-697473927  

[foo.zip](https://github.com/boostorg/python/files/5268906/foo.zip)  
Add forgotten example.

---

## Comment 2

> Username: colinRawlings  
> Created at: 2020-12-07 09:12:32 UTC  
> Url: https://github.com/boostorg/python/issues/317#issuecomment-739784686  

I believe this issue is related: https://github.com/boostorg/python/issues/278
