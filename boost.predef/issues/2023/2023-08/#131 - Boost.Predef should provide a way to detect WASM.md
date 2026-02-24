# #131 - Boost.Predef should provide a way to detect WASM [Open]

> Username: jcelerier  
> Created at: 2023-08-10 15:47:12 UTC  
> Updated at: 2025-02-04 20:50:16 UTC  
> Url: https://github.com/boostorg/predef/issues/131  

Relevant macros gathered from `echo | em++ -dM -E -x c++ -`:  
```c++  
#define EMSCRIPTEN 1  
#define __EMSCRIPTEN__ 1  
#define __wasm 1  
#define __wasm32 1  
#define __wasm32__ 1  
#define __wasm__ 1  
```

---

## Comment 1

> Username: jcelerier  
> Created at: 2023-08-10 15:50:03 UTC  
> Url: https://github.com/boostorg/predef/issues/131#issuecomment-1673486656  

Also it's already detected in some way by boost.config: https://www.boost.org/doc/libs/1_82_0/boost/config/detail/select_platform_config.hpp

---

## Comment 2

> Username: sleeptightAnsiC  
> Created at: 2025-02-03 18:12:23 UTC  
> Updated at: 2025-02-04 20:49:05 UTC  
> Url: https://github.com/boostorg/predef/issues/131#issuecomment-2631721681  

This would require adding targets for `wasm32` and `wasm64`, and then adding `Emscripten` as a compiler (or as platform) and ensuring it does not clash with Clang/GCC. [EDIT] Also SIMD detection which seems a bit tricky: https://emscripten.org/docs/porting/simd.html  
  
boost.config doesn't seem to do anything specific with WASM, it just treats it as POSIX environment.  
  
I wonder if there are any C/C++ compilers capable of targeting WASM other than Clang and its derivatives?

---

## Comment 3

> Username: sleeptightAnsiC  
> Created at: 2025-02-03 18:43:22 UTC  
> Updated at: 2025-02-04 20:50:16 UTC  
> Url: https://github.com/boostorg/predef/issues/131#issuecomment-2631783752  

Also what kind of PLAT and OS should be assigned for this? boost.config just ignores this and does not assign anything specific. I've seen some projects simply referring to this as "HTML5" (or "WEB") and it could be probably fine as PLAT but Emscripten seems to be referring to itself as a platform which complicates everything a bit. This check is fine to skip as boost.predef allows for this but I wonder what user of WASM/Emscripten would expect? When running emcc with predef it already detects OS as unix but I'm not sure if this is correct.   
  
<details><summary>[comparison: emcc -dumpmachine]</summary>  
<p>  
  
```  
$ emcc -dumpmachine  
wasm32-unknown-emscripten  
  
$ emcc -dumpmachine --target=wasm64  
wasm64-unknown-emscripten  
  
$ clang -dumpmachine --target=wasm64  
wasm64  
  
$ x86_64-w64-mingw32-gcc -dumpmachine  
x86_64-w64-mingw32  
```  
  
</p>  
</details>   
  
<details><summary>[comparison: emcc AVAILABLE macros]</summary>  
<p>  
  
```  
$ git clone https://github.com/boostorg/predef.git  
  
$ echo '#include "boost/predef.h"' > main.c  
$ echo 'int main(void) {return 0;}' >> main.c  
  
$ clang -dM -E main.c -I./predef/include | grep BOOST | grep AVAILABLE | grep -v NOT_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_32 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_64 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_X86 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_X86_64 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_X86_64_AVAILABLE   
#define BOOST_ARCH_X86_AVAILABLE   
#define BOOST_COMP_CLANG_AVAILABLE   
#define BOOST_COMP_GNUC_AVAILABLE   
#define BOOST_COMP_LLVM_AVAILABLE   
#define BOOST_COMP_LLVM_DETECTION BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE   
#define BOOST_HW_SIMD_AVAILABLE   
#define BOOST_HW_SIMD_X86_AVAILABLE   
#define BOOST_LANG_STDC_AVAILABLE   
#define BOOST_LIB_C_GNU_AVAILABLE   
#define BOOST_OS_LINUX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_LINUX_AVAILABLE   
#define BOOST_OS_UNIX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_UNIX_AVAILABLE   
#define BOOST_PREDEF_TESTED_AT(symbol,major,minor,patch) ( (symbol) >= BOOST_VERSION_NUMBER_AVAILABLE )  
#define BOOST_VERSION_NUMBER_AVAILABLE BOOST_VERSION_NUMBER_MIN  
  
$ clang --target=wasm32 -dM -E main.c -I./predef/include | grep BOOST | grep AVAILABLE | grep -v NOT_AVAILABLE  
In file included from main.c:1:  
In file included from ./include/boost/predef.h:16:  
In file included from ./include/boost/predef/library.h:13:  
In file included from ./include/boost/predef/library/c.h:13:  
In file included from ./include/boost/predef/library/c/_prefix.h:11:  
./include/boost/predef/detail/_cassert.h:14:10: fatal error: 'assert.h' file not found  
   14 | #include <assert.h>  
      |          ^~~~~~~~~~  
1 error generated.                                                                                                                                                                                                                  
#define BOOST_ARCH_WORD_BITS_32 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_COMP_CLANG_AVAILABLE   
#define BOOST_COMP_GNUC_AVAILABLE   
#define BOOST_COMP_LLVM_AVAILABLE   
#define BOOST_COMP_LLVM_DETECTION BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE   
#define BOOST_LANG_STDC_AVAILABLE   
#define BOOST_PREDEF_TESTED_AT(symbol,major,minor,patch) ( (symbol) >= BOOST_VERSION_NUMBER_AVAILABLE )  
#define BOOST_VERSION_NUMBER_AVAILABLE BOOST_VERSION_NUMBER_MIN  
  
$ emcc -dM -E main.c -I./predef/include | grep BOOST | grep AVAILABLE | grep -v NOT_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_32 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_COMP_CLANG_AVAILABLE   
#define BOOST_COMP_GNUC_AVAILABLE   
#define BOOST_COMP_LLVM_AVAILABLE   
#define BOOST_COMP_LLVM_DETECTION BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE   
#define BOOST_LANG_STDC_AVAILABLE   
#define BOOST_OS_UNIX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_UNIX_AVAILABLE   
#define BOOST_PREDEF_TESTED_AT(symbol,major,minor,patch) ( (symbol) >= BOOST_VERSION_NUMBER_AVAILABLE )  
#define BOOST_VERSION_NUMBER_AVAILABLE BOOST_VERSION_NUMBER_MIN  
```  
  
</p>  
</details>
