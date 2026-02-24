# #106 - Install issue [Closed]

> Username: smalls12  
> Created at: 2020-05-31 22:00:22 UTC  
> Updated at: 2020-06-01 00:51:11 UTC  
> Closed at: 2020-06-01 00:51:11 UTC  
> Url: https://github.com/boostorg/json/issues/106  

Hello,  
  
Trying to compile BeastLounge and buildling this library as it is a dependency.  
  
Getting the following error:  
```  
CMake Error at CMakeLists.txt:114 (install):  
  install TARGETS given no ARCHIVE DESTINATION for static library target  
  "boost_json".  
```  
  
I'm guessing this might be a specific issue for my system  
  
Ubuntu 18.04  
gcc 7.5.0  
cmake 3.10.2

---

## Comment 1

> Username: qis  
> Created at: 2020-05-31 23:06:10 UTC  
> Url: https://github.com/boostorg/json/issues/106#issuecomment-636544287  

@smalls12 Hello. Would you be so kind and test if #107 resolves your problem?

---

## Comment 2

> Username: smalls12  
> Created at: 2020-05-31 23:53:43 UTC  
> Url: https://github.com/boostorg/json/issues/106#issuecomment-636550022  

Yup fixed.  
Thanks :)
