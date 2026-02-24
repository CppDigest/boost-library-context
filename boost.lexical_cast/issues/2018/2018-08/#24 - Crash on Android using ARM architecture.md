# #24 - Crash on Android using ARM architecture [Closed]

> Username: rcdailey  
> Created at: 2018-08-17 14:02:03 UTC  
> Updated at: 2018-08-17 14:17:52 UTC  
> Closed at: 2018-08-17 14:17:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/24  

I'm using Boost 1.68. I compiled it using NDK r17b. I used the following `user-config.jam`. Note I did all of this on Windows using Command Prompt.  
  
```  
import os ;  
  
local toolchain_arm = "C:/android/ndk-standalone/r17b-arm" ;  
  
using clang : android_arm : "$(toolchain_arm)/bin/clang++.cmd"  
:  
<archiver>"$(toolchain_arm)/bin/arm-linux-androideabi-ar.exe"  
<ranlib>"$(toolchain_arm)/bin/arm-linux-androideabi-ranlib.exe"  
;  
```  
  
I created the standalone toolchains for the NDK using this command:  
  
    make_standalone_toolchain.py --arch arm --api 15 --stl libc++ --install-dir C:/android/ndk-standalone/r17b-arm  
  
And I built boost using this command:  
  
    .\tools\build\b2 -j8 --without-python --without-test toolset=clang-android_arm variant=release target-os=android threading=multi threadapi=pthread link=static runtime-link=shared cxxstd=14 architecture=arm address-model=32 binary-format=elf abi=aapcs stage  
  
When I run my native code on Android, which uses this version of boost, I get a SIGSEGV with this callstack:  
  
```  
<unknown> 0x0000000000000000  
boost::exception_detail::refcount_ptr<boost::exception_detail::error_info_container>::release() exception.hpp:86  
boost::exception_detail::refcount_ptr<boost::exception_detail::error_info_container>::~refcount_ptr() exception.hpp:42  
boost::exception::~exception() exception.hpp:306  
boost::exception_detail::error_info_injector<boost::bad_lexical_cast>::~error_info_injector() exception.hpp:360  
void boost::throw_exception<boost::bad_lexical_cast>(boost::bad_lexical_cast const&) throw_exception.hpp:69  
void boost::conversion::detail::throw_bad_cast<std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> >, unsigned int>() bad_lexical_cast.hpp:92  
unsigned int boost::lexical_cast<unsigned int, std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > >(std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > const&) lexical_cast.hpp:42  
```  
  
The code using lexical_cast is doing, basically:  
  
    boost::lexical_cast<unsigned>("");  
  
This causes a `bad_lexical_cast` to be thrown; but in the middle of throwing that exception, the segfault happens. I don't have any further analysis to share, and I'm not even certain what the root cause is. Does the information I provided offer any hints? I'm not sure if this is a bug, and I'm not sure if the lexical cast repo is the best place to ask the question since boost.exception shows up in the call stack too.

---

## Comment 1

> Username: rcdailey  
> Created at: 2018-08-17 14:17:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/24#issuecomment-413880462  

Actually please allow me to close this for now. I am seeing similar crashes during stack unwinding after throwing exceptions in code not related to boost. I think this might be an NDK issue. Let me investigate some more. If I find out otherwise I'll reopen. Sorry for the confusion.
