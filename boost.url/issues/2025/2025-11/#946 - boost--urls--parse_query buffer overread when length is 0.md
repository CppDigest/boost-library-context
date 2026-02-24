# #946 - boost::urls::parse_query buffer overread when length is 0 [Closed]

> Username: jeremiahar  
> Created at: 2025-11-08 12:13:58 UTC  
> Updated at: 2025-12-10 04:22:27 UTC  
> Closed at: 2025-11-13 17:04:13 UTC  
> Url: https://github.com/boostorg/url/issues/946  

boost::urls::parse_query overreads from the input core::string_view when the length is specified as 0.   
  
https://github.com/boostorg/url/blob/boost-1.89.0/src/parse_query.cpp#L31  
  
```c++  
#include <boost/url/parse_query.hpp>  
  
int main(int argc, char **argv)  
{  
    char *c = new char[1];  
    c[0] = 'a';  
      
    std::string_view s = std::string_view(c, 0);  
    auto result = boost::urls::parse_query(s);  
    return 0;  
}  
  
```  
  
```  
[jeremiah@jeremiah boost_url]$ clang++ -std=c++23 -O0 -g -fsanitize=address,undefined -lboost_url main.cpp -o main  
[jeremiah@jeremiah boost_url]$ ./main  
=================================================================  
==3299059==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x708df6de0011 at pc 0x5687917139a3 bp 0x7ffefe622c50 sp 0x7ffefe622420  
READ of size 2 at 0x708df6de0011 thread T0  
    #0 0x5687917139a2 in strlen.part.0 asan_interceptors.cpp.o  
    #1 0x746df818dfd8 in std::char_traits<char>::length(char const*) /usr/include/c++/15.2.1/bits/char_traits.h:393:25  
    #2 0x746df818dfd8 in boost::core::basic_string_view<char>::basic_string_view(char const*) /usr/src/debug/boost/boost_1_89_0/./boost/core/detail/string_view.hpp:375:106  
    #3 0x746df818dfd8 in boost::urls::parse_query(boost::core::basic_string_view<char>) /usr/src/debug/boost/boost_1_89_0/libs/url/src/parse_query.cpp:32:23  
    #4 0x568791815b5f in main /tmp/boost_url/main.cpp:9:19  
    #5 0x746df7a27674 in __libc_start_call_main /usr/src/debug/glibc/glibc/csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
    #6 0x746df7a27728 in __libc_start_main /usr/src/debug/glibc/glibc/csu/../csu/libc-start.c:360:3  
    #7 0x5687916bf154 in _start (/tmp/boost_url/main+0x30154) (BuildId: 2e4f9af5ea0ca32fd8a28a34ae24bf99facfdc60)  
  
0x708df6de0011 is located 0 bytes after 1-byte region [0x708df6de0010,0x708df6de0011)  
allocated by thread T0 here:  
    #0 0x56879181419d in operator new[](unsigned long) (/tmp/boost_url/main+0x18519d) (BuildId: 2e4f9af5ea0ca32fd8a28a34ae24bf99facfdc60)  
    #1 0x568791815a9a in main /tmp/boost_url/main.cpp:5:15  
    #2 0x746df7a27674 in __libc_start_call_main /usr/src/debug/glibc/glibc/csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
    #3 0x746df7a27728 in __libc_start_main /usr/src/debug/glibc/glibc/csu/../csu/libc-start.c:360:3  
    #4 0x5687916bf154 in _start (/tmp/boost_url/main+0x30154) (BuildId: 2e4f9af5ea0ca32fd8a28a34ae24bf99facfdc60)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow asan_interceptors.cpp.o in strlen.part.0  
Shadow bytes around the buggy address:  
  0x708df6ddfd80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x708df6ddfe00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x708df6ddfe80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x708df6ddff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x708df6ddff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x708df6de0000: fa fa[01]fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x708df6de0080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x708df6de0100: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x708df6de0180: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x708df6de0200: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x708df6de0280: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
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
==3299059==ABORTING  
  
```  
  
It's the same bug as https://github.com/boostorg/url/issues/757 , but this time it's in the s.empty() branch. The problem also occurs if you pass a default constructed string_view.

---

## Comment 1

> Username: Parmbassi  
> Created at: 2025-12-10 04:22:27 UTC  
> Url: https://github.com/boostorg/url/issues/946#issuecomment-3635317750  

Hope this finds you well; There are still many players worldwide looking for updates on Zombsroyale.io, could we be expecting anything soon? Any response is fine.
