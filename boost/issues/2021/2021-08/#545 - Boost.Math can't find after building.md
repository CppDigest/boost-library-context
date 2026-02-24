# #545 - Boost.Math can't find after building [Closed]

> Username: mahuichao  
> Created at: 2021-08-09 06:26:17 UTC  
> Updated at: 2021-08-09 07:31:22 UTC  
> Closed at: 2021-08-09 07:31:22 UTC  
> Url: https://github.com/boostorg/boost/issues/545  

Hello , I am new to Boost.   
     I want to use Boost.Math and estimate SNR, but after installing Boost, I can't find Math library ,just the headers in include. Can someone tell me how to build with match correctly? thanks

---

## Comment 1

> Username: mahuichao  
> Created at: 2021-08-09 06:39:31 UTC  
> Url: https://github.com/boostorg/boost/issues/545#issuecomment-894984845  

the version is :1.76  
system:centos  
my cmd:   
    1. cd boost  
    2. ./bootstrap --prefix=xxxx  
    3. ./b2 --with-math install   
 but no include or lib in prefix directory
