# #165 Fixes build errors on gcc < 5.2 and mcst compilers [Closed]

> Username: Arenoros  
> Created at: 2024-02-19 05:01:02 UTC  
> Updated at: 2024-02-21 10:40:17 UTC  
> Closed at: 2024-02-21 10:30:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/165  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-02-19 05:07:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/165#pullrequestreview-1887561838  

📁 build/Jamfile

```diff
   9 | 
  10 |- local SOURCES = from_chars.cpp to_chars.cpp ;
  10 |+ local SOURCES = from_chars.cpp to_chars.cpp dragonbox.cpp;
```

> Username: pdimov  
> Created_at: 2024-02-19 05:07:54 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#discussion_r1494014701  

You need a space before the semicolon here. The jam/b2 language requires whitespace between tokens.


---

## Comment 2

> Username: mborland  
> Created_at: 2024-02-20 09:12:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1953775720  

How much older than GCC 5.2 are you trying to make this work for? You can add the following lines to `.drone.jsonnnet` to cover the GCC 4.X versions:  
  
```  
linux_pipeline(  
        "Linux 14.04 GCC 4.8* 32/64",  
        "cppalliance/droneubuntu1404:1",  
        { TOOLSET: 'gcc', COMPILER: 'g++', CXXSTD: '03,11', ADDRMD: '32,64' },  
    ),  
  
    linux_pipeline(  
        "Linux 14.04 GCC 4.9 32/64",  
        "cppalliance/droneubuntu1404:1",  
        { TOOLSET: 'gcc', COMPILER: 'g++-4.9', CXXSTD: '03,11', ADDRMD: '32,64' },  
        "g++-4.9-multilib",  
        [ "ppa:ubuntu-toolchain-r/test" ],  
    ),  
```  
  
For historical context support for GCC 4.X were dropped specifically for this issue.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-02-20 10:11:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1953880797  

That's not going to work because b2 no longer supports building under 14.04. It should be something like  
```  
linux_pipeline(  
        "Linux 16.04 GCC 4.8 32/64",  
        "cppalliance/droneubuntu1604:1",  
        { TOOLSET: 'gcc', COMPILER: 'g++-4.8', CXXSTD: '03,11', ADDRMD: '32,64' },  
        "g++-4.8-multilib",  
    ),  
  
    linux_pipeline(  
        "Linux 16.04 GCC 4.9 32/64",  
        "cppalliance/droneubuntu1604:1",  
        { TOOLSET: 'gcc', COMPILER: 'g++-4.9', CXXSTD: '03,11', ADDRMD: '32,64' },  
        "g++-4.9-multilib",  
    ),  
```

---

## Comment 4

> Username: Arenoros  
> Created_at: 2024-02-20 13:26:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1954215145  

cross build for armv7-linux-gcc 4.8.2

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-02-20 13:31:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1954224444  

There seems to be something wrong with msvc-14.0 (VS 2015), though.

---

## Comment 6

> Username: Arenoros  
> Created_at: 2024-02-20 14:25:00 UTC  
> Updated_at: 2024-02-20 14:26:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1954326419  

the error is due to the warning that dragonbox.cpp does not create obj files because it should not create anything for msvc, in 4.9/4.8 the problem is with the conversion warning, I don't know how relevant they are at all. Is it possible for individual targets to ignore these warnings or is there a way to fix them?

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-02-20 14:39:39 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1954356812  

```  
compile-c-c++ bin.v2\libs\charconv\build\msvc-14.0\debug\address-model-32\cxxstd-14-iso\link-static\threading-multi\dragonbox.obj  
dragonbox.cpp  
libs\charconv\build\..\src\dragonbox.cpp(9): error C2373: 'cache': redefinition; different type modifiers  
D:\a\charconv\boost-root\boost/charconv/detail/dragonbox/dragonbox_common.hpp(771): note: see declaration of 'cache'  
libs\charconv\build\..\src\dragonbox.cpp(14): error C2373: 'cache': redefinition; different type modifiers  
D:\a\charconv\boost-root\boost/charconv/detail/dragonbox/dragonbox.hpp(860): note: see declaration of 'cache'  
```  
This is an error, not a warning.  
  
The -Wconversion warnings are unrelated to this PR. Since we didn't have 4.8/4.9 in CI, we haven't noticed those. Matt may be able to do something about them if he decides to support 4.8/4.9.

---

## Comment 8

> Username: mborland  
> Created_at: 2024-02-21 10:30:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1956346372  

Since you seem to be having similar build problems as I did I am going to close this. Frankly I am not interested in adding the maintenance burden of GCC 4.X to the library, and I do not want you to waste your time.

---

## Comment 9

> Username: Arenoros  
> Created_at: 2024-02-21 10:40:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/165#issuecomment-1956365498  

let me finalize my theories, because it is not realistic for me to deploy all tests locally, I just want to achieve that all tests pass successfully, and the further decision to include or not is not very important to me.

---
