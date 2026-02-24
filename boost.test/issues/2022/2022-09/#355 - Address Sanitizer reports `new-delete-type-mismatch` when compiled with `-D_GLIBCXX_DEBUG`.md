# #355 - Address Sanitizer reports `new-delete-type-mismatch` when compiled with `-D_GLIBCXX_DEBUG` [Closed]

> Username: mkoncek  
> Created at: 2022-09-19 15:36:46 UTC  
> Updated at: 2022-09-20 14:09:18 UTC  
> Closed at: 2022-09-20 14:09:18 UTC  
> Url: https://github.com/boostorg/test/issues/355  

## Versions  
`gcc-12.2.1-1.fc38.x86_64`  
`boost-test-1.78.0-9.fc37.x86_64`  
`libasan-12.2.1-1.fc38.x86_64`  
  
## How to reproduce  
Compile this code:  
```c++  
#define BOOST_TEST_MODULE test  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(test) {}  
```  
  
With the following flags:  
`$ g++ test.cpp -fsanitize=address -D_GLIBCXX_DEBUG -lboost_unit_test_framework`  
Note that it is `-D_GLIBCXX_DEBUG` that causes the problem.  
  
After running it, you will get output:  
```  
Running 1 test case...  
  
*** No errors detected  
=================================================================  
==763708==ERROR: AddressSanitizer: new-delete-type-mismatch on 0x615000000080 in thread T0:  
  object passed to delete has wrong type:  
  size of the allocated type:   472 bytes;  
  size of the deallocated type: 312 bytes.  
    #0 0x7f1afd98a088 in operator delete(void*, unsigned long) (/lib64/libasan.so.8+0xbc088)  
    #1 0x7f1afd8748c9 in boost::unit_test::framework::state::~state() (/lib64/libboost_unit_test_framework.so.1.78.0+0x318c9)  
    #2 0x7f1afd372414 in __run_exit_handlers (/lib64/libc.so.6+0x3b414)  
    #3 0x7f1afd37258f in __GI_exit (/lib64/libc.so.6+0x3b58f)  
    #4 0x7f1afd35aa56 in __libc_start_call_main (/lib64/libc.so.6+0x23a56)  
    #5 0x7f1afd35ab08 in __libc_start_main_impl (/lib64/libc.so.6+0x23b08)  
    #6 0x403364 in _start (/tmp/a.out+0x403364)  
  
0x615000000080 is located 0 bytes inside of 472-byte region [0x615000000080,0x615000000258)  
allocated by thread T0 here:  
    #0 0x7f1afd989188 in operator new(unsigned long) (/lib64/libasan.so.8+0xbb188)  
    #1 0x404c2c in boost::unit_test::make_test_case(boost::function<void ()> const&, boost::unit_test::basic_cstring<char const>, boost::unit_test::basic_cstring<char const>, unsigned long) (/tmp/a.out+0x404c2c)  
    #2 0x4045c3 in __static_initialization_and_destruction_0(int, int) (/tmp/a.out+0x4045c3)  
    #3 0x4046b2 in _GLOBAL__sub_I__Z14init_unit_testv (/tmp/a.out+0x4046b2)  
    #4 0x7f1afd35ab7d in __libc_start_main_impl (/lib64/libc.so.6+0x23b7d)  
  
SUMMARY: AddressSanitizer: new-delete-type-mismatch (/lib64/libasan.so.8+0xbc088) in operator delete(void*, unsigned long)  
==763708==HINT: if you don't care about these errors you may set ASAN_OPTIONS=new_delete_type_mismatch=0  
==763708==ABORTING  
```  
  
This is not a critical issue but it floods the text output.

---

## Comment 1

> Username: mkoncek  
> Created at: 2022-09-19 15:54:12 UTC  
> Updated at: 2022-09-19 15:54:53 UTC  
> Url: https://github.com/boostorg/test/issues/355#issuecomment-1251213464  

FYI with debuginfo, the stack trace refers to these lines of file `boost/test/impl/framework.ipp`:  
* `~state() { clear(); }`  
* `delete static_cast<test_case const*>(tu_ptr);`  
  
Compiling with `-D_GLIBCXX_DEBUG` changes the sizes of standard containers or of their iterators. That is probably the issue.

---

## Comment 2

> Username: mkoncek  
> Created at: 2022-09-20 14:09:18 UTC  
> Url: https://github.com/boostorg/test/issues/355#issuecomment-1252411096  

I realized that the problem is on my side by using a dynamically linked library. I don't think this can be fixed in any way.
