# #294 - Syntax error in inline assembly when targeting armv7 [Closed]

> Username: j-jorge  
> Created at: 2024-12-25 12:59:22 UTC  
> Updated at: 2024-12-30 19:55:27 UTC  
> Closed at: 2024-12-30 19:55:27 UTC  
> Url: https://github.com/boostorg/unordered/issues/294  

Hello, I encounter an issue when compiling the armeabi-v7a library of my Android project. The problem can be reproduced by compiling a file with this content:  
  
```c++  
// test.cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
```  
  
The compilation fails with the following message:  
  
```  
<inline asm>:1:41: error: expected '%<type>' or "<type>"  
    1 | .pushsection ".debug_gdb_scripts", "MS",@progbits,1  
      |                                         ^  
<inline asm>:318:12: error: .popsection without corresponding .pushsection  
  318 | .popsection  
      |            ^  
2 errors generated.  
```  
  
The command used to compile the file is:  
```  
path/to/ndk/27.2.12479018/toolchains/llvm/prebuilt/linux-x86_64/bin/clang++ \  
  --target=armv7-none-linux-androideabi21 \  
  --sysroot=path/to/ndk/27.2.12479018/toolchains/llvm/prebuilt/linux-x86_64/sysroot \  
  -Ipath/to/include/ \  
  -fvisibility=hidden \  
  -fdata-sections \  
  -ffunction-sections \  
  -funwind-tables \  
  -fstack-protector-strong \  
  -no-canonical-prefixes \  
  -D_FORTIFY_SOURCE=2 \  
  -march=armv7-a \  
  -mthumb \  
  -fexceptions \  
  -frtti \  
  -stdlib=libc++ \  
  -g \  
  -fno-limit-debug-info \  
  -std=c++20 \  
  -fPIC \  
  -fcolor-diagnostics -pthread \  
  -MD \  
  -MT \  
  test.o \  
  -c test.cpp  
```  
  
Boost 1.87.0 is installed in the path/to/include directory passed to the `-I` option. There is no problem with the arm64 nor the x86_64 architectures.  
  
As a workaround I can compile with `-DBOOST_ALL_NO_EMBEDDED_GDB_SCRIPTS` but I which I did not have to do that (i.e. that the inline assembly would be omitted when the target does not support it).  
  
Note that it happens here with Boost.Unordered but I expect a similar problem with Boost.Interprocess and Boost.Json as they also use this GDB macro :)

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-12-26 11:28:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/294#issuecomment-2562530400  

Related llvm/llvm-project#120871

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-12-26 11:34:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/294#issuecomment-2562530407  

Looking at the workaround linked in that issue, another option is to use `-fno-integrated-as` to use system assembler rather than the integrated Clang assembler.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-12-26 11:36:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/294#issuecomment-2562530410  

Apparently, this fixes it: https://github.com/ned14/outcome/pull/308.

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-12-26 11:56:20 UTC  
> Url: https://github.com/boostorg/unordered/issues/294#issuecomment-2562561302  

> Note on targets where the @ character is the start of a comment (eg ARM) then another character is used instead. For example the ARM port uses the % character.  
  
(https://www.sourceware.org/binutils/docs/as/Section.html)  
  
So, this is actually the expected behaviour. Interestingly, I don't see universal support for `%` as section type prefix documented, but it appears to be the case. I tested a few targets on CE.
