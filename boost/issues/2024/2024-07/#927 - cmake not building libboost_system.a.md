# #927 - cmake not building libboost_system.a [Closed]

> Username: Duron27  
> Created at: 2024-07-04 16:04:39 UTC  
> Updated at: 2024-07-12 13:02:13 UTC  
> Closed at: 2024-07-12 13:02:13 UTC  
> Url: https://github.com/boostorg/boost/issues/927  

not sure whats going on but the title says it all

---

## Comment 1

> Username: Duron27  
> Created at: 2024-07-04 16:31:23 UTC  
> Url: https://github.com/boostorg/boost/issues/927#issuecomment-2209322769  

it's strange because after running cmake the libs folder has all libraries including system and they all have "CMakeFiles  Makefile  cmake_install.cmake" in the directory.  
just using cmake . builds everything except system.

---

## Comment 2

> Username: Duron27  
> Created at: 2024-07-12 13:02:08 UTC  
> Url: https://github.com/boostorg/boost/issues/927#issuecomment-2225539666  

so libboost_system.a is just a dummy file with a header in it. Using the NDK the command   
llvm-ar rc libboost_system.a $(find / -name "error_code.o" 2>/dev/null) builds the library fine. or just ar if youre not using the NDK
