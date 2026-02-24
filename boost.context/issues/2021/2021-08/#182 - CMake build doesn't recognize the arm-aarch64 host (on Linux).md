# #182 - CMake build doesn't recognize the arm/aarch64 host (on Linux) [Closed]

> Username: leha-bot  
> Created at: 2021-08-09 23:26:46 UTC  
> Updated at: 2021-08-14 15:50:01 UTC  
> Closed at: 2021-08-14 15:50:01 UTC  
> Url: https://github.com/boostorg/context/issues/182  

It seems that the CMakeLists.txt simply does not support yet (according to [these files](https://github.com/boostorg/context/blob/561a31508f02889f9b18063156c4b019e9f827d7/CMakeLists.txt#L41-L47)).  
I have a fix for this issue using the `CMAKE_SYSTEM_ARCHITECTURE` variable.   
If you don't mind, I will make a PR with fix.  
   
Thanks for your attention and your continuous work on Boost! 😊
