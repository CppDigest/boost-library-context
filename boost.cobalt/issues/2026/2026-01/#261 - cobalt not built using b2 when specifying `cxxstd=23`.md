# #261 - cobalt not built using b2 when specifying `cxxstd=23` [Open]

> Username: jcmonnin  
> Created at: 2026-01-08 07:57:39 UTC  
> Updated at: 2026-01-08 11:57:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/261  

On Windows with MSVC, it took me a while to figure out that I need to revert from C++23 to C++20 mode if I want the cobalt libs to be built.  
  
This doesn't build cobalt  
`b2 cxxstd=23 --with-cobalt` (see [1])  
  
But this works  
`b2 cxxstd=20 --with-cobalt` (see [2])  
  
To me it looks more like a b2 issue, but post it here as I know nothing about the b2 build system and encountered this issue when wanting to use cobalt.  
  
Also note the `warning: non-free usage requirements <boost.cobalt.pmr>std ignored` which I'm not sure if it's fine to ignore or not (as described in #245)  
  
I use `b2` out of habit, but is it recommended to use CMake to build boost these days?  
  
----  
[1] in C++23 mode  
```  
b2 cxxstd=23 --with-cobalt  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
  
Building the Boost C++ Libraries.  
  
  
    - cxx20_hdr_concepts       : no [2]  
    - cxx20_hdr_concepts       : no [3]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [2]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [2]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [2]  
    - BOOST_ARCH_X86           : no [2]  
    - BOOST_ARCH_IA64          : no [2]  
    - BOOST_ARCH_SPARC         : no [2]  
    - BOOST_ARCH_LOONGARCH     : no [2]  
    - BOOST_ARCH_MIPS          : no [2]  
    - BOOST_ARCH_PARISC        : no [2]  
    - BOOST_ARCH_ARM           : no [2]  
    - BOOST_ARCH_RISCV         : no [2]  
    - BOOST_ARCH_PPC           : no [2]  
    - BOOST_ARCH_SYS390        : no [2]  
    - ssl                      : no [2]  
    - cxx20_hdr_concepts       : no [4]  
    - cxx20_hdr_concepts       : no [5]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [4]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [4]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [4]  
    - BOOST_ARCH_X86           : no [4]  
    - BOOST_ARCH_IA64          : no [4]  
    - BOOST_ARCH_SPARC         : no [4]  
    - BOOST_ARCH_LOONGARCH     : no [4]  
    - BOOST_ARCH_MIPS          : no [4]  
    - BOOST_ARCH_PARISC        : no [4]  
    - BOOST_ARCH_ARM           : no [4]  
    - BOOST_ARCH_RISCV         : no [4]  
    - BOOST_ARCH_PPC           : no [4]  
    - BOOST_ARCH_SYS390        : no [4]  
    - ssl                      : no [4]  
    - cxx20_hdr_concepts       : no [6]  
    - cxx20_hdr_concepts       : no [7]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [6]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [6]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [6]  
    - BOOST_ARCH_X86           : no [6]  
    - BOOST_ARCH_IA64          : no [6]  
    - BOOST_ARCH_SPARC         : no [6]  
    - BOOST_ARCH_LOONGARCH     : no [6]  
    - BOOST_ARCH_MIPS          : no [6]  
    - BOOST_ARCH_PARISC        : no [6]  
    - BOOST_ARCH_ARM           : no [6]  
    - BOOST_ARCH_RISCV         : no [6]  
    - BOOST_ARCH_PPC           : no [6]  
    - BOOST_ARCH_SYS390        : no [6]  
    - ssl                      : no [6]  
    - cxx20_hdr_concepts       : no [8]  
    - cxx20_hdr_concepts       : no [9]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [8]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [8]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [8]  
    - BOOST_ARCH_X86           : no [8]  
    - BOOST_ARCH_IA64          : no [8]  
    - BOOST_ARCH_SPARC         : no [8]  
    - BOOST_ARCH_LOONGARCH     : no [8]  
    - BOOST_ARCH_MIPS          : no [8]  
    - BOOST_ARCH_PARISC        : no [8]  
    - BOOST_ARCH_ARM           : no [8]  
    - BOOST_ARCH_RISCV         : no [8]  
    - BOOST_ARCH_PPC           : no [8]  
    - BOOST_ARCH_SYS390        : no [8]  
    - ssl                      : no [8]  
```  
  
[2] in C++20 mode  
```  
b2 cxxstd=20 --with-cobalt  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - cxx20_hdr_concepts       : yes [2]  
    - cpp_lib_memory_resource  : yes [2]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [2]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [2]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [2]  
    - BOOST_ARCH_X86           : no [2]  
    - BOOST_ARCH_IA64          : no [2]  
    - BOOST_ARCH_SPARC         : no [2]  
    - BOOST_ARCH_LOONGARCH     : no [2]  
    - BOOST_ARCH_MIPS          : no [2]  
    - BOOST_ARCH_PARISC        : no [2]  
    - BOOST_ARCH_ARM           : no [2]  
    - BOOST_ARCH_RISCV         : no [2]  
    - BOOST_ARCH_PPC           : no [2]  
    - BOOST_ARCH_SYS390        : no [2]  
warning: non-free usage requirements <boost.cobalt.pmr>std ignored  
warning: in main-target boost_cobalt at C:\Users\jean-claude\Documents\src\boost_1_90_0\libs\cobalt\build\Jamfile:80  
    - ssl                      : no [2]  
    - cxx20_hdr_concepts       : yes [3]  
    - cpp_lib_memory_resource  : yes [3]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [3]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [3]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [3]  
    - BOOST_ARCH_X86           : no [3]  
    - BOOST_ARCH_IA64          : no [3]  
    - BOOST_ARCH_SPARC         : no [3]  
    - BOOST_ARCH_LOONGARCH     : no [3]  
    - BOOST_ARCH_MIPS          : no [3]  
    - BOOST_ARCH_PARISC        : no [3]  
    - BOOST_ARCH_ARM           : no [3]  
    - BOOST_ARCH_RISCV         : no [3]  
    - BOOST_ARCH_PPC           : no [3]  
    - BOOST_ARCH_SYS390        : no [3]  
    - ssl                      : no [3]  
    - cxx20_hdr_concepts       : yes [4]  
    - cpp_lib_memory_resource  : yes [4]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [4]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [4]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [4]  
    - BOOST_ARCH_X86           : no [4]  
    - BOOST_ARCH_IA64          : no [4]  
    - BOOST_ARCH_SPARC         : no [4]  
    - BOOST_ARCH_LOONGARCH     : no [4]  
    - BOOST_ARCH_MIPS          : no [4]  
    - BOOST_ARCH_PARISC        : no [4]  
    - BOOST_ARCH_ARM           : no [4]  
    - BOOST_ARCH_RISCV         : no [4]  
    - BOOST_ARCH_PPC           : no [4]  
    - BOOST_ARCH_SYS390        : no [4]  
    - ssl                      : no [4]  
    - cxx20_hdr_concepts       : yes [5]  
    - cpp_lib_memory_resource  : yes [5]  
    - BOOST_ARCH_WORD_BITS == 0.0.16 : no [5]  
    - BOOST_ARCH_WORD_BITS == 0.0.32 : no [5]  
    - BOOST_ARCH_WORD_BITS == 0.0.64 : no [5]  
    - BOOST_ARCH_X86           : no [5]  
    - BOOST_ARCH_IA64          : no [5]  
    - BOOST_ARCH_SPARC         : no [5]  
    - BOOST_ARCH_LOONGARCH     : no [5]  
    - BOOST_ARCH_MIPS          : no [5]  
    - BOOST_ARCH_PARISC        : no [5]  
    - BOOST_ARCH_ARM           : no [5]  
    - BOOST_ARCH_RISCV         : no [5]  
    - BOOST_ARCH_PPC           : no [5]  
    - BOOST_ARCH_SYS390        : no [5]  
    - ssl                      : no [5]  
 ```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2026-01-08 11:28:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/261#issuecomment-3723446824  

`b2` is still the main build system. What's interesting is that the concept check fails for C++23:  
  
```  
    - cxx20_hdr_concepts       : no [2]  
```  
  
Which is what cobalt is using to determine if C++20 is available.  
  
The test is [here](https://github.com/boostorg/config/blob/3d072615495357ff450a77ac8c43e060a75455ef/test/boost_no_cxx20_hdr_concepts.ipp#L9), does that compile for you?

---

## Comment 2

> Username: jcmonnin  
> Created at: 2026-01-08 11:57:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/261#issuecomment-3723543595  

Thanks for the quick reply. Yes if I place the file you linked somewhere in my project, it compiles fine with the same MSVC compiler in C++23 mode.  
  
I did some further test by enabling verbosity in b2 build  
  
With `b2 cxxstd=20` the compiler flag `/std:c++20` is passed to `cl` as expected  
```  
 cl /Zm800 -nologo "C:\Users\jean-claude\Documents\src\boost_1_90_0\libs\cobalt\src\detail\exception.cpp" -c -Fo"bin.v2\libs\cobalt\build\msvc-14.3\release\x86_64\cxxstd-20-iso\link-static\threading-multi\detail\exception.obj"     -TP /wd4675 /EHs /std:c++20 /GR /Zc:throwingNew /O2 /Ob2 /W3 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /favor:blend /bigobj -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_SOURCE=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTAINER_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DDATE_TIME_INLINE -DNDEBUG -DWIN32_LEAN_AND_MEAN -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_WARNINGS "-I." "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\asio\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\assert\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\cobalt\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\container\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\container_hash\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\context\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\core\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\describe\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\endian\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\integer\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\intrusive\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\move\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\mp11\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\pool\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\predef\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\preprocessor\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\smart_ptr\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\static_assert\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\static_string\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\system\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\throw_exception\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\type_traits\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\utility\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\variant2\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\winapi\include" "-Ilibs\config\include"  
```  
  
with `b2 cxxstd=23` I cannot observe a compiler flag to set the C++ standard  
```  
  cl /Zm800 -nologo "C:\Users\jean-claude\Documents\src\boost_1_90_0\libs\atomic\src\find_address_sse2.cpp" -c -Fo"bin.v2\libs\atomic\build\msvc-14.3\debug\x86_64\cxxstd-23-iso\link-static\threading-multi\find_address_sse2.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_SOURCE -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_USE_WINDOWS_H "-I." "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\assert\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\atomic\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\atomic\src" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\predef\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\preprocessor\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\static_assert\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\type_traits\include" "-IC:\Users\jean-claude\Documents\src\boost_1_90_0\libs\winapi\include" "-Ilibs\config\include"  
```
