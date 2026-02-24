# #254 - Undefined behaviour in filter_iterator [Open]

> Username: sehe  
> Created at: 2022-04-20 03:17:45 UTC  
> Updated at: 2022-09-16 23:33:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/254  

Filter_iterator doesn't check against the end iterator in its internal advance logic (this is a feature that `boost::iterator::filter_iterator` adds).  
  
The problem creates trouble whenever the input sequence ends in an element that doesn't satisfy the filter predicate:  
  
```#include <boost/archive/iterators/remove_whitespace.hpp>  
#include <iomanip>  
#include <iostream>  
  
namespace bai = boost::archive::iterators;  
  
int main() {  
    using It = bai::remove_whitespace<const char*>;  
    std::string const s = "oops "; // ends in whitespace, causes UB  
  
    std::string filtered(It(s.c_str()), It(s.c_str() + s.length()));  
    std::cout << std::quoted(filtered) << std::flush;  
}  
```  
  
Live with ASAN: https://compiler-explorer.com/z/xYaoqq7hs  
  
````  
=================================================================  
==1==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffcb5cda040 at pc 0x00000040390d bp 0x7ffcb5cd9d60 sp 0x7ffcb5cd9d58  
READ of size 1 at 0x7ffcb5cda040 thread T0  
    #0 0x40390c in dereference_impl /opt/compiler-explorer/libs/boost_1_78_0/boost/archive/iterators/remove_whitespace.hpp:105  
    #1 0x40390c in dereference /opt/compiler-explorer/libs/boost_1_78_0/boost/archive/iterators/remove_whitespace.hpp:113  
    #2 0x40390c in dereference<boost::archive::iterators::filter_iterator<(anonymous namespace)::remove_whitespace_predicate<char>, char const*> > /opt/compiler-explorer/libs/boost_1_78_0/boost/iterator/iterator_facade.hpp:550  
    #3 0x40390c in operator* /opt/compiler-explorer/libs/boost_1_78_0/boost/iterator/iterator_facade.hpp:656  
    #4 0x40390c in void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<boost::archive::iterators::remove_whitespace<char const*> >(boost::archive::iterators::remove_whitespace<char const*>, boost::archive::iterators::remove_whitespace<char const*>, std::input_iterator_tag) /opt/compiler-explorer/gcc-trunk-20220419/include/c++/12.0.1/bits/basic_string.tcc:204  
    #5 0x40390c in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string<boost::archive::iterators::remove_whitespace<char const*>, void>(boost::archive::iterators::remove_whitespace<char const*>, boost::archive::iterators::remove_whitespace<char const*>, std::allocator<char> const&) /opt/compiler-explorer/gcc-trunk-20220419/include/c++/12.0.1/bits/basic_string.h:756  
    #6 0x40390c in main /app/example.cpp:11  
    #7 0x7f3716fc50b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x240b2)  
    #8 0x4041ed in _start (/app/output.s+0x4041ed)  
  
Address 0x7ffcb5cda040 is located in stack of thread T0 at offset 576 in frame  
    #0 0x40245f in main /app/example.cpp:7  
  
  This frame has 21 object(s):  
    [32, 33) '<unknown>'  
    [48, 49) '<unknown>'  
    [64, 65) '<unknown>'  
    [80, 81) '<unknown>'  
    [96, 97) '<unknown>'  
    [112, 113) '<unknown>'  
    [128, 136) 'start'  
    [160, 168) 'start'  
    [192, 200) '__guard'  
    [224, 232) 'start'  
    [256, 264) 'start'  
    [288, 296) '__capacity'  
    [320, 328) '__guard'  
    [352, 368) '<unknown>'  
    [384, 400) '<unknown>'  
    [416, 432) '<unknown>'  
    [448, 464) '<unknown>'  
    [480, 496) '<unknown>'  
    [512, 528) '<unknown>'  
    [544, 576) 's' (line 9) <== Memory access at offset 576 overflows this variable  
    [608, 640) 'filtered' (line 11)  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-buffer-overflow /opt/compiler-explorer/libs/boost_1_78_0/boost/archive/iterators/remove_whitespace.hpp:105 in dereference_impl  
Shadow bytes around the buggy address:  
  0x100016b933b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100016b933c0: f1 f1 f1 f1 f8 f2 01 f2 f8 f2 f8 f2 f8 f2 01 f2  
  0x100016b933d0: 00 f2 f2 f2 00 f2 f2 f2 f8 f2 f2 f2 00 f2 f2 f2  
  0x100016b933e0: 00 f2 f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 00 f2 f2  
  0x100016b933f0: 00 00 f2 f2 00 00 f2 f2 00 00 f2 f2 00 00 f2 f2  
=>0x100016b93400: 00 00 f2 f2 00 00 00 00[f2]f2 f2 f2 00 00 00 00  
  0x100016b93410: f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100016b93420: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100016b93430: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100016b93440: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100016b93450: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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
==1==ABORTING  
```

---

## Comment 1

> Username: sehe  
> Created at: 2022-04-20 10:02:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/254#issuecomment-1103744435  

The corresponding interface in Boost Iterator has the end iterator check correcty (allthough it's still [easy to use wrong](https://github.com/boostorg/iterator/issues/71))

---

## Comment 2

> Username: robertramey  
> Created at: 2022-09-16 23:21:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/254#issuecomment-1249931818  

Is this still pending?  can I close this now?  If not, what is the best fix?

---

## Comment 3

> Username: sehe  
> Created at: 2022-09-16 23:33:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/254#issuecomment-1249935838  

Yes, this is still a bug. The best fix is to add the missing iterator checks. Second best would to remove the iterator classes from the documented interface as they are a bug trap.  
  
[In practice, I think they're practically only tested in the configuration used for Serialization's base64 support, which is very constrained compared to the documented promises of the iterator adaptors in Boost Serialization]
