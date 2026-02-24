# #50 - call boost::system::error_code's message(),but crash [Closed]

> Username: guanzhongdaoke  
> Created at: 2019-03-19 08:06:33 UTC  
> Updated at: 2020-06-16 21:09:34 UTC  
> Closed at: 2020-06-16 21:09:34 UTC  
> Url: https://github.com/boostorg/system/issues/50  

### when i call boost::system::error_code's message() to get some error information in boost asio,bug the application crashed.  
  
boost：Version 1.69.0.  
os：Linux VM_0_10_centos 3.10.0-693.el7.x86_64 boostorg/boost#1 SMP Tue Aug 22 21:09:27 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux  
gcc：gcc version 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)   
  
the code like this:  
![code](https://user-images.githubusercontent.com/14351169/54589266-29cb0e80-4a60-11e9-8a31-958939835abb.png)  
  
the print log like this:  
![print](https://user-images.githubusercontent.com/14351169/54589378-76164e80-4a60-11e9-8033-8889400ba90c.png)  
  
the core dump stack is:  
![dump](https://user-images.githubusercontent.com/14351169/54589400-829aa700-4a60-11e9-97fe-5166abbf13e7.png)  
![core](https://user-images.githubusercontent.com/14351169/54596917-9221ec00-4a70-11e9-9059-1e677571e879.png)  
  
  
  
so,it crashed when i call error.message(),before boost 1.67.0,that is ok,so i guess that in boost 1.69.0,boost::system::error_category::equivalent(boost::system::error_code const&, int) const () has some wrong.

---

## Comment 1

> Username: guanzhongdaoke  
> Created at: 2019-03-19 12:35:22 UTC  
> Url: https://github.com/boostorg/system/issues/50#issuecomment-474343423  

look at the print log,"Error=111" means that the connect failed,  
and this code run good on windows,just crash in linux.  
with the core dump,the crash stack,i think maybe some error in boost/system/error_code.hpp

---

## Comment 2

> Username: pdimov  
> Created at: 2019-12-17 19:47:10 UTC  
> Url: https://github.com/boostorg/system/issues/50#issuecomment-566720251  

`message` doesn't call `equivalent`. I suspect that the crashing `error_code` comes from a separately compiled library that is still using an earlier Boost and hasn't been recompiled with 1.69.
