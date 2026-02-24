# #217 - managed_map_file find_or_construct does not return Cache aligned memory [Closed]

> Username: billzheng  
> Created at: 2024-04-23 04:45:43 UTC  
> Updated at: 2024-10-03 08:10:46 UTC  
> Closed at: 2024-10-03 08:06:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/217  

```  
struct alignas( 64 ) type_t {  
  int arr[1000];  
};  
  
namespace bip = boost::interprocess;  
typedef bip::basic_managed_mapped_file<  
    char,  
    bip::rbtree_best_fit<bip::mutex_family, bip::offset_ptr<void>,  
                         64>,  
    bip::iset_index>  
    managed_mapped_file_aligned;  
  
inline void allocate() {  
  managed_mapped_file_aligned mmf( bip::open_only,  
                                   "/mnt/hugepage/test_boost_shm" );  
  
  type_t* ptr = mmf.find_or_construct<type_t>( "test name" )();  
  // ptr is not CACHE aligned  
}  
```

---

## Comment 1

> Username: sehe  
> Created at: 2024-04-23 11:47:42 UTC  
> Url: https://github.com/boostorg/interprocess/issues/217#issuecomment-2072084707  

Minified and extrapolating for analysis: https://godbolt.org/z/Eh4K7hnKE  
  
```c++  
#include <boost/interprocess/managed_heap_memory.hpp>  
namespace bip = boost::interprocess;  
using Seg = bip::basic_managed_heap_memory<char,                                                                //  
       bip::rbtree_best_fit<bip::mutex_family, bip::offset_ptr<void>, 64>,  
       bip::iset_index>;  
  
struct alignas(64) T { int arr[1000]; };  
static void test(auto key) { Seg(10240).construct<T>(key)(); }  
  
int main() {  
    test(bip::anonymous_instance); // OKAY  
    test(bip::unique_instance);    // Not OKAY  
    test("data");                  // Not OKAY  
}  
```  
  
Printing  
  
```c++  
boost/interprocess/detail/named_proxy.hpp:90:49: runtime error: constructor call on misaligned address 0x526000000298 for type 'T', which requires 64 byte alignment  
0x526000000298: note: pointer points here  
 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/interprocess/detail/named_proxy.hpp:90:49 in  
```  
  
I might have time to look closer later.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-09-26 21:17:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/217#issuecomment-2377955646  

Reviewing old issues:  
  
The problem is that the underlying heap memory (operator new) is not allocating memory that is 64 byte aligned. Not sure how we should fix it. Maybe managed_heap_memory should take the alignment value from the memory allocation algorithm and use aligned new or similar utilities to guarantee that memory is properly aligned.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-10-03 08:06:22 UTC  
> Updated at: 2024-10-03 08:10:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/217#issuecomment-2390781390  

Many thanks for the report and the test case!  
  
For Boost 1.87 Interprocess will implement a new ABI that fixes this issue honoring the overalignment requests. The commit implementing the new ABI is https://github.com/boostorg/interprocess/commit/cbeb5b5ea9d363484306b33c0a0ad304914ed3f3 with additional space optimizations in https://github.com/boostorg/interprocess/commit/94bc4e11860d3be216d1c1c248c4cef000d2fc96  
  
Unfortunately it was not possible to fix the bug without breaking ABI, you can #define BOOST_INTERPROCESS_SEGMENT_MANAGER_ABI to 1 to use the old, broken ABI, that will remain in the library for some releases.
