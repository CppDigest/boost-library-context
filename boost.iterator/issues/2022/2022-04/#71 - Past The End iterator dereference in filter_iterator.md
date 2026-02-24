# #71 - Past The End iterator dereference in filter_iterator [Open]

> Username: sehe  
> Created at: 2022-04-20 03:05:03 UTC  
> Updated at: 2022-04-20 19:54:30 UTC  
> Url: https://github.com/boostorg/iterator/issues/71  

This simple test program segfaults on my GCC box with Boost 1.78  
  
```c++  
#include <boost/iterator/filter_iterator.hpp>  
#include <iostream>  
#include <array>  
  
int main() {  
    struct False { bool operator()(unsigned char) const { return false; } };  
  
    std::array<char, 3> const s{'A','B','C'};  
    using FIt = boost::iterators::filter_iterator<False, const char*>;  
  
    for (FIt it(False{}, s.begin(), s.end()), end(s.end()); it != end; ++it) {  
        std::cout << static_cast<int>(*it) << " ";  
    };  
}  
```  
  
See it live with ASAN: https://godbolt.org/z/8c9f1drGn  
  
```  
=================================================================  
==1==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffc00329dc3 at pc 0x0000004fc947 bp 0x7ffc00329d90 sp 0x7ffc00329d88  
READ of size 1 at 0x7ffc00329dc3 thread T0  
    #0 0x4fc946 in main /app/example.cpp:12:39  
    #1 0x7f90b8b920b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x240b2)  
    #2 0x41f34d in _start (/app/output.s+0x41f34d)  
  
Address 0x7ffc00329dc3 is located in stack of thread T0 at offset 35 in frame  
    #0 0x4fc853 in main /app/example.cpp:5  
  
  This frame has 1 object(s):  
    [32, 35) 's' (line 8) <== Memory access at offset 35 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-buffer-overflow /app/example.cpp:12:39 in main  
Shadow bytes around the buggy address:  
  0x10000005d360: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d370: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d380: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d390: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d3a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x10000005d3b0: 00 00 00 00 f1 f1 f1 f1[03]f3 f3 f3 00 00 00 00  
  0x10000005d3c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d3d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d3e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d3f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10000005d400: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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

> Username: grisumbras  
> Created at: 2022-04-20 06:16:39 UTC  
> Url: https://github.com/boostorg/iterator/issues/71#issuecomment-1103511313  

You create the end iterator incorrectly. You need to pass the base end iterator twice.

---

## Comment 2

> Username: sehe  
> Created at: 2022-04-20 10:30:20 UTC  
> Updated at: 2022-04-20 10:31:37 UTC  
> Url: https://github.com/boostorg/iterator/issues/71#issuecomment-1103777050  

@grisumbras Good spot. Confirmed clean: https://godbolt.org/z/65TYrMe44  
  
I think that the defaulted value is questionable - not PitOfSuccess design.  
  
 - it should probably only ever be defaulted for InputIterator category iterators,   
 - if at all, because it's just as simple to write `make_filter_iterator(pred, input_it, {})` for over a decade now  
  
If it's a sentinel anyways, there might be better ways of constructing the iterator. Right now `make_filter_iterator` doesn't make the usage simpler, even assuming c++11 for `auto` type deduction:  
  
```c++  
// c++11  
auto first = boost::make_filter_iterator(False{}, begin(s), end(s)),  
     last  = boost::make_filter_iterator(first.predicate(), first.end(), first.end());  
  
for (; first != last; ++first) {  
    std::cout << static_cast<int>(*first) << " ";  
};  
```  
  
Arguably a c++03(!) version not using `make_filter_iterator` os less clunky:  
  
```c++  
// c++03  
boost::filter_iterator<False, char const*> //  
    first(False(), s.begin(), s.end()),    //  
    last(first.end(), first.end());  
  
for (; first != last; ++first) {  
    std::cout << static_cast<int>(*first) << " ";  
};  
```  
  
And of course c++17 allows:  
  
```c++  
    // c++17  
    boost::filter_iterator                  //  
        first(False(), s.begin(), s.end()), //  
        last(first.predicate(), first.end(), first.end());  
  
    for (; first != last; ++first) {  
        std::cout << static_cast<int>(*first) << " ";  
    };  
```  
  
The common problem is that the `last` iterator can and **must** be derived from exactly the info from the `first` info. Ideally, one would have an `make_end_iterator(it)` helper that Does The Right Thing. Or, like with some other iterator adaptors, we could just accept that the interface is an implementation detail for Range Adaptors only:  
  
```c++  
    // boost range  
    using boost::adaptors::filtered;  
  
    for (auto ch : s | filtered(False{})) {  
        std::cout << static_cast<int>(ch) << " ";  
    };  
```  
  
Compare all four versions: https://godbolt.org/z/dqcdEv5cE  
  
## TL;DR  
  
I'd say in the light of the above, the defaulted end-iterator value doesn't pull its weight and should not be defaulted.

---

## Comment 3

> Username: sehe  
> Created at: 2022-04-20 19:54:30 UTC  
> Url: https://github.com/boostorg/iterator/issues/71#issuecomment-1104399730  

Motivating example that's **extremely** easy to use wrong (and really _should not_ compile IMO): https://stackoverflow.com/a/71933410/85371
