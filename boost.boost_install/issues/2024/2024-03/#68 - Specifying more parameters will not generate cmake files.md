# #68 - [boost1.68]Specifying more parameters will not generate cmake files [Closed]

> Username: cctv130  
> Created at: 2024-03-23 15:40:19 UTC  
> Updated at: 2024-03-24 05:33:27 UTC  
> Closed at: 2024-03-24 05:33:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/68  

How to specify the command to generate the cmake files in lib in b2?

---

## Comment 1

> Username: cctv130  
> Created at: 2024-03-24 02:10:52 UTC  
> Updated at: 2024-03-24 02:11:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/68#issuecomment-2016682012  

There is no cmake directory  
![1711246242999](https://github.com/boostorg/cmake/assets/133784576/7f6566b3-5f67-439b-b64b-163b44b05be1)

---

## Comment 2

> Username: pdimov  
> Created at: 2024-03-24 03:41:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/68#issuecomment-2016682013  

You need Boost 1.70.0 at minimum to generate the CMake files. 1.68 doesn't yet have this functionality.
