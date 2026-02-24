# #258 - Block cache holds dangling pointers from custom new, then regex_search corrupts memory [Open]

> Username: nlguillemot  
> Created at: 2025-08-09 09:17:48 UTC  
> Updated at: 2025-08-10 10:40:05 UTC  
> Url: https://github.com/boostorg/regex/issues/258  

# Problem  
  
The global memory block cache does not consider the possibility that operator new could be temporarily overridden.  
  
This means that the global memory block cache can erroneously hold onto pointers from temporary allocators.  
  
# Repro  
  
The example below illustrates how disabling the memory block cache fixes the issue.  
  
If you remove the `#define` that disables the cache, then it crashes with eg. "double free or corruption"  
  
https://coliru.stacked-crooked.com/a/417637f462a33d56  
  
```cpp  
// Remove this line to cause a crash.  
#define BOOST_REGEX_MAX_CACHE_BLOCKS 0  
  
#include <boost/regex.hpp>  
#include <cstddef>  
#include <cstdlib>  
  
struct StackAlloc {  
    char* buf;  
    size_t off = 0;  
    StackAlloc() { buf = (char*)malloc(64 * 1024); }  
    ~StackAlloc() { free(buf); }  
    void* alloc(size_t n) { off=(off+7)&~7; void* p=buf+off; off+=n; return p; }  
    void dealloc(void*) {}  
};  
  
thread_local StackAlloc* current_alloc;  
  
void* operator new(size_t n) {  
    return current_alloc ? current_alloc->alloc(n) : malloc(n);  
}  
void operator delete(void* p) noexcept {  
    current_alloc ? current_alloc->dealloc(p) : free(p);  
}  
  
int main() {  
    boost::regex rx("(a+)(b*)");  
    {  
        StackAlloc a;  
        current_alloc = &a;  
        boost::regex_search("aaaaabbbb", rx);  
        current_alloc = nullptr;  
    }  
    boost::regex_search("aaaaabbbb", rx);  
}  
```  
  
# Comparison  
  
This issue does not happen with std::regex because implementations of std::regex don't use a global memory block cache.  
  
# Suggestion  
  
My suggestion is to disable the cache by default, and include a warning in the documentation about enabling it.  
  
Alternatively, I suggest to put a clear warning about the std::regex incompatibility in the documentation.

---

## Comment 1

> Username: nlguillemot  
> Created at: 2025-08-09 09:37:46 UTC  
> Url: https://github.com/boostorg/regex/issues/258#issuecomment-3170548012  

Here's the output from ASAN when running the repro without disabling the cache.  
  
```  
=================================================================  
==45084==ERROR: AddressSanitizer: heap-use-after-free on address 0x11a280021818 at pc 0x7ff6147b370f bp 0x002a4b1af3c0 sp 0x002a4b1af3c8  
WRITE of size 4 at 0x11a280021818 thread T0  
    #0 0x7ff6147b370e in boost::re_detail_500::saved_state::saved_state \boost.1.87.0\lib\native\include\boost\regex\v5\perl_matcher_non_recursive.hpp:52  
    #1 0x7ff6147b340e in boost::re_detail_500::save_state_init::save_state_init \boost.1.87.0\lib\native\include\boost\regex\v5\perl_matcher_non_recursive.hpp:103  
    #2 0x7ff6147fe9eb in boost::re_detail_500::perl_matcher<char const *,std::allocator<boost::sub_match<char const *> >,boost::regex_traits<char,boost::w32_regex_traits<char> > >::find_imp \boost.1.87.0\lib\native\include\boost\regex\v5\perl_matcher_common.hpp:234  
    #3 0x7ff6147fe8ee in boost::re_detail_500::perl_matcher<char const *,std::allocator<boost::sub_match<char const *> >,boost::regex_traits<char,boost::w32_regex_traits<char> > >::find \boost.1.87.0\lib\native\include\boost\regex\v5\perl_matcher_common.hpp:216  
    #4 0x7ff6147a14f3 in boost::regex_search<char const *,char,boost::regex_traits<char,boost::w32_regex_traits<char> > > \boost.1.87.0\lib\native\include\boost\regex\v5\regex_search.hpp:80  
    #5 0x7ff6147a124a in boost::regex_search<char,boost::regex_traits<char,boost::w32_regex_traits<char> > > \boost.1.87.0\lib\native\include\boost\regex\v5\regex_search.hpp:88  
    #6 0x7ff614771771 in main \main.cpp:39  
    #7 0x7ff614897f18 in invoke_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:78  
    #8 0x7ff614897e61 in __scrt_common_main_seh D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
    #9 0x7ff614897d1d in __scrt_common_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:330  
    #10 0x7ff614897f8d in mainCRTStartup D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:16  
    #11 0x7ffce437e8d6 in BaseThreadInitThunk+0x16 (C:\WINDOWS\System32\KERNEL32.DLL+0x18002e8d6)  
    #12 0x7ffce51fc34b in RtlUserThreadStart+0x2b (C:\WINDOWS\SYSTEM32\ntdll.dll+0x18003c34b)  
  
0x11a280021818 is located 4120 bytes inside of 65536-byte region [0x11a280020800,0x11a280030800)  
freed by thread T0 here:  
    #0 0x7ffba525d408 in _asan_wrap__CrtIsValidHeapPointer+0xb78 (C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.43.34808\bin\HostX64\x64\clang_rt.asan_dynamic-x86_64.dll+0x18004d408)  
    #1 0x7ff6147b8609 in StackAlloc::~StackAlloc \main.cpp:12  
    #2 0x7ff61477172f in main \main.cpp:38  
    #3 0x7ff614897f18 in invoke_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:78  
    #4 0x7ff614897e61 in __scrt_common_main_seh D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
    #5 0x7ff614897d1d in __scrt_common_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:330  
    #6 0x7ff614897f8d in mainCRTStartup D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:16  
    #7 0x7ffce437e8d6 in BaseThreadInitThunk+0x16 (C:\WINDOWS\System32\KERNEL32.DLL+0x18002e8d6)  
    #8 0x7ffce51fc34b in RtlUserThreadStart+0x2b (C:\WINDOWS\SYSTEM32\ntdll.dll+0x18003c34b)  
  
previously allocated by thread T0 here:  
    #0 0x7ffba525d558 in _asan_wrap__CrtIsValidHeapPointer+0xcc8 (C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.43.34808\bin\HostX64\x64\clang_rt.asan_dynamic-x86_64.dll+0x18004d558)  
    #1 0x7ff6147b07f1 in StackAlloc::StackAlloc \main.cpp:11  
    #2 0x7ff61477150b in main \main.cpp:34  
    #3 0x7ff614897f18 in invoke_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:78  
    #4 0x7ff614897e61 in __scrt_common_main_seh D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
    #5 0x7ff614897d1d in __scrt_common_main D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:330  
    #6 0x7ff614897f8d in mainCRTStartup D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:16  
    #7 0x7ffce437e8d6 in BaseThreadInitThunk+0x16 (C:\WINDOWS\System32\KERNEL32.DLL+0x18002e8d6)  
    #8 0x7ffce51fc34b in RtlUserThreadStart+0x2b (C:\WINDOWS\SYSTEM32\ntdll.dll+0x18003c34b)  
  
SUMMARY: AddressSanitizer: heap-use-after-free \boost.1.87.0\lib\native\include\boost\regex\v5\perl_matcher_non_recursive.hpp:52 in boost::re_detail_500::saved_state::saved_state  
Shadow bytes around the buggy address:  
  0x11a280021580: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021600: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021680: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021700: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021780: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
=>0x11a280021800: fd fd fd[fd]fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021880: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021900: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021980: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021a00: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x11a280021a80: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07  
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
```
