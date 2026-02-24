# #216 - CMake configuration fails on Debian Sid with MinGW [Closed]

> Username: niansa  
> Created at: 2023-01-20 17:40:33 UTC  
> Updated at: 2023-01-20 19:37:23 UTC  
> Closed at: 2023-01-20 19:37:22 UTC  
> Url: https://github.com/boostorg/context/issues/216  

Hi!  
  
I am attempting to use beast and asio with MinGW on Debian Sid, however I am getting this error during configuration:  
  
    CMake Error at /home/nils/Projects/build-dpplogger-MinGW_x64-Debug/_deps/boost-src/libs/context/CMakeLists.txt:30 (math):  
    math cannot parse the expression: "*8": syntax error, unexpected exp_TIMES  
    (1).  
  
Weirdly enough it says:  
  
    -- Boost.Context: architecture x86_64, binary format elf, ABI sysv, assembler gas, suffix .S, implementation fcontext  
  
Right underneath :thinking:   
  
My CMake version is `3.25.1` and my GCC version is `x86_64-w64-mingw32-gcc (GCC) 12-win32`  
  
Niansa

---

## Comment 1

> Username: niansa  
> Created at: 2023-01-20 19:37:22 UTC  
> Url: https://github.com/boostorg/context/issues/216#issuecomment-1398845047  

Alright, nevermind, Debians mingw is just broken
