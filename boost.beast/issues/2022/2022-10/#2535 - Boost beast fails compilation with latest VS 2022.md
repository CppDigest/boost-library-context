# #2535 - Boost beast fails compilation with latest VS 2022 [Closed]

> Username: pps83  
> Created at: 2022-10-10 13:25:20 UTC  
> Updated at: 2023-01-31 16:13:48 UTC  
> Closed at: 2023-01-31 16:13:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2535  

### Version of Beast  
  
BOOST_BEAST_VERSION 330  
  
### Steps necessary to reproduce the problem  
  
default project with VS 2022 will fail if something tries to include boost beast with errors that look like this:  
  
```  
1>C:\boost_1_80_0\boost\beast\core\detail\type_traits.hpp(67,32): error C4996: 'std::aligned_storage<8,8>': warning STL4034: std::aligned_storage and std::aligned_storage_t are deprecated in C++23. Prefer alignas(T) std::byte t_buff[sizeof(T)]. You can define _SILENCE_CXX23_ALIGNED_STORAGE_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to acknowledge that you have received this warning. (compiling source file ..\src\test\HttpTest.cpp)  
1>C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.33.31629\include\type_traits(1070,1): message : see declaration of 'std::aligned_storage' (compiling source file ..\src\test\HttpTest.cpp)  
```  
  
### All relevant compiler information  
  
Latest VS 2022

---

## Comment 1

> Username: pps83  
> Created at: 2022-10-10 13:27:00 UTC  
> Updated at: 2022-10-10 13:27:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2535#issuecomment-1273312087  

Check the [1_80 log](https://www.boost.org/users/history/version_1_80_0.html) to see `aligned_storage` that's being removed from libraries to support latest c++

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-10 13:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2535#issuecomment-1273314363  

You're compiling on a standard that's not yet released and aligned_storage is only deprecated. I.e. that's not an error, but a warning. So I don't think that'll be addressed soon.
