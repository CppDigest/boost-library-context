# #112 - Second Definition Warning [Closed]

> Username: vermissa0ss  
> Created at: 2023-08-22 15:51:32 UTC  
> Updated at: 2023-08-23 21:38:10 UTC  
> Closed at: 2023-08-23 21:38:10 UTC  
> Url: https://github.com/boostorg/system/issues/112  

I am building library A that links to library B and C. Libraries B and C individually link to boost system library. The problem is I get second definition warning\  
`B.lib(error_code.obj) : warning LNK4006: "void __cdecl boost::system::dummy_exported_function(void)" (?dummy_exported_function@system@boost@@YAXXZ) already defined in C.lib(error_code.obj); second definition ignored [C:\Gitlab-Runner\builds\Jdss\0\myFiles\A_Wrapper\A_Wrapper_prog\A_lib.vcxproj]`  
  
I'm using boost 1.72 on windows 10 with Visual Studio Build Engine version 16.7.0  
  
I have tried boost 1.82 as suggested by [this](https://github.com/conan-io/conan-center-index/issues/4008) that newer version would fix this similar problem but still received the warning.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-08-22 22:29:43 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1689010514  

You don't need to compile `error_code.cpp` into `B.lib` or `C.lib`. Boost.System is header-only and this source file is only used when building a stub library for backward compatibility.

---

## Comment 2

> Username: vermissa0ss  
> Created at: 2023-08-23 00:12:46 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1689080742  

Thank you for the response. I appreciate the insight, but I want to clarify that I'm not manually compiling the individual files of the Boost libraries. I'm building them using Boost's own bootstrap process, which doesn't allow me to exclude individual files like error_code.cpp.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-08-23 03:49:09 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1689238477  

I'll need more information on how to reproduce the error, then.

---

## Comment 4

> Username: vermissa0ss  
> Created at: 2023-08-23 19:27:48 UTC  
> Updated at: 2023-08-23 19:28:15 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1690520487  

[boost_tests.zip](https://github.com/boostorg/system/files/12422613/boost_tests.zip)  
File above contains three projects.  
`boost_test_C` is a UDP transmit using `asio`.\  
`boost_test_B` is UDP receive using `asio`  
`boost_test_A` link to library `boost_test_C` and `boost_test_B`.  
  
NOTE: Please change the directory for `<AdditionalLibraryDirectories>` and `<AdditionalIncludeDirectories>` from `boost_test_A.vcxproj` and `boost_test_B.vcxproj` to match where your boost header and libraries are.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-08-23 20:24:41 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1690588742  

All three libraries compile when I remove the "Additional Dependencies" from boost_test_B and boost_test_C.

---

## Comment 6

> Username: vermissa0ss  
> Created at: 2023-08-23 21:38:10 UTC  
> Url: https://github.com/boostorg/system/issues/112#issuecomment-1690679623  

Thank you for taking the time to look at those files. Our team missed the transition when Boost became header-only. I will close the issue now
