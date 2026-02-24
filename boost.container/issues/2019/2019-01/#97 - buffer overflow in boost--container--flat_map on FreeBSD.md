# #97 - buffer overflow in boost::container::flat_map on FreeBSD [Closed]

> Username: arthaud  
> Created at: 2019-01-04 20:14:47 UTC  
> Updated at: 2019-01-07 00:02:23 UTC  
> Closed at: 2019-01-06 23:39:15 UTC  
> Url: https://github.com/boostorg/container/issues/97  

Hi everyone,  
  
I found a bug in `boost::container::flat_map` on FreeBSD.  
  
System: FreeBSD 12.0  
Compiler: Clang 6.0.1 with libc++ (default compiler on FreeBSD)  
Boost: master branch on Github  
  
See the following example:  
```c++  
#include <cstdio>  
#include <boost/container/flat_map.hpp>  
  
int main() {  
  boost::container::flat_map<std::pair<char, char>, char> map;  
  map.emplace(std::make_pair(1, 2), 3);  
  printf("%d, %d, %d\n",  
    map.begin()->first.first,  
    map.begin()->first.second,  
    map.begin()->second);  
  return 0;  
}  
```  
**Output**: `2, 3, 0`  
**Expected**: `1, 2, 3`  
  
`flat_map` uses `boost::container::dtl::pair` internally and uses reinterpret_casts to expose a `std::pair` in the iterators, see [flat_map.hpp#L66](https://github.com/boostorg/container/blob/a9ceb4d9c60fedbd01e388331a0f4a12d92c2941/include/boost/container/flat_map.hpp#L66) and [flat_map.hpp#L547](https://github.com/boostorg/container/blob/a9ceb4d9c60fedbd01e388331a0f4a12d92c2941/include/boost/container/flat_map.hpp#L547)  
  
The problem is that `std::pair` and `boost::container::dtl::pair` have a different memory layout!  
  
See the following example:  
```c++  
#include <cstdio>  
#include <boost/container/flat_map.hpp>  
  
int main() {  
  std::pair< std::pair< char, char >, char > std_pair;  
  boost::container::dtl::pair< std::pair< char, char >, char > boost_pair;  
  printf("%lu %p %p\n", sizeof(std_pair), &std_pair, &std_pair.first);  
  printf("%lu %p %p\n", sizeof(boost_pair), &boost_pair, &boost_pair.first);  
  return 0;  
}  
```  
**Output**:  
```  
4 0x7fffffffe738 0x7fffffffe739  
3 0x7fffffffe730 0x7fffffffe730  
```  
  
`std::pair` has a weird padding of 1 byte at the beginning. I think this is related to this patch: https://reviews.llvm.org/D25389  
I think there might be a problem with the base class `__non_trivially_copyable_base`. It looks like the EBO doesn't work properly.  
  
I'm not sure what to do to fix this. I think the C++ standard doesn't enforce the layout of `std::pair` to be `{first, second}`, so maybe boost shouldn't assume this.  
  
To reproduce this, I used the following virtual machine:  
https://download.freebsd.org/ftp/releases/VM-IMAGES/12.0-RELEASE/amd64/Latest/  
Upstream bug in IKOS: https://github.com/NASA-SW-VnV/ikos/issues/22

---

## Comment 1

> Username: arthaud  
> Created at: 2019-01-05 02:17:56 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451619878  

It looks like this will be fixed in libc++: https://bugs.llvm.org/show_bug.cgi?id=40230

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-01-05 22:15:53 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451695831  

Thanks for the report. This nasty pair class was used to portably support all compilers (including C++03 compilers, where std::pair had no move semantics or placement constructors.) One workaround is to embed an internal std::pair inside the dtl::pair class to guarantee excatly the same memory layout.   
  
The second step would be to use the native std::pair on compilers withou full C++11 support.  
  
I'll try the first workaround in the following days and inform about progress in this bug.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2019-01-05 23:45:46 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451702099  

The first workaround is not possible because pair exposes "first" and "second" as public members. We can try to reproduce the padding in libcpp with the following patch. Could please test it, as I haven't been able to set up the virtual machine yet?  
[boost_container_libcpp.txt](https://github.com/boostorg/container/files/2729996/boost_container_libcpp.txt)

---

## Comment 4

> Username: arthaud  
> Created at: 2019-01-06 04:21:21 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451714197  

Thanks for the patch. Unfortunately I'm getting the following error:  
  
```  
$ c++ test.cpp -I/tmp/boost/include  
In file included from test.cpp:3:  
In file included from /tmp/boost/include/container/include/boost/container/flat_map.hpp:29:  
In file included from /tmp/boost/include/container/include/boost/container/detail/flat_tree.hpp:29:  
/tmp/boost/include/container/include/boost/container/detail/pair.hpp:211:67: error: too many arguments provided to function-like macro invocation  
   : pair_padding<T1, T2, offsetof(std::pair<T1 BOOST_MOVE_I T2>, first) >  
                                                                  ^  
/usr/include/sys/cdefs.h:476:9: note: macro '__offsetof' defined here  
#define __offsetof(type, field)  __builtin_offsetof(type, field)  
        ^  
In file included from test.cpp:3:  
In file included from /tmp/boost/include/container/include/boost/container/flat_map.hpp:29:  
In file included from /tmp/boost/include/container/include/boost/container/detail/flat_tree.hpp:29:  
/tmp/boost/include/container/include/boost/container/detail/pair.hpp:211:27: error: use of undeclared identifier '__offsetof'  
   : pair_padding<T1, T2, offsetof(std::pair<T1 BOOST_MOVE_I T2>, first) >  
                          ^  
/usr/include/stddef.h:75:31: note: expanded from macro 'offsetof'  
#define offsetof(type, field)   __offsetof(type, field)  
                                ^  
In file included from test.cpp:3:  
In file included from /tmp/boost/include/container/include/boost/container/flat_map.hpp:29:  
In file included from /tmp/boost/include/container/include/boost/container/detail/flat_tree.hpp:29:  
/tmp/boost/include/container/include/boost/container/detail/pair.hpp:213:1: error: expected class name  
{  
^  
3 errors generated.  
```  
  
Regarding the patch, I would like to suggest to check for `_LIBCPP_DEPRECATED_ABI_DISABLE_PAIR_TRIVIAL_COPY_CTOR` instead of `_LIBCPP_VERSION`. This will avoid possible regressions on most systems and save compile time.

---

## Comment 5

> Username: arthaud  
> Created at: 2019-01-06 04:25:56 UTC  
> Updated at: 2019-01-06 04:42:16 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451714350  

It compiles with `-std=c++11` but the bug is still there, I'm trying to investigate this.  
  
EDIT: It was an error on my side. It still doesn't compile with `-std=c++11`  
  
EDIT 2: It looks like a common macro problem, where the comma in `std::pair< T1, T2 >` is treated as an argument separator for the macro `offset_of`. This is probably why you tried to use `BOOST_MOVE_I` but it didn't work.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2019-01-06 12:32:44 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451738121  

I've reproduced the problem in Linux using clang -stdlib=libc++ and defining "_LIBCPP_DEPRECATED_ABI_DISABLE_PAIR_TRIVIAL_COPY_CTOR".  
  
New patch attached, the patch   
[boost_container_pair_libcpp_patch.txt](https://github.com/boostorg/container/files/2730460/boost_container_pair_libcpp_patch.txt)  
compiles in C++03 and C++2a modes.

---

## Comment 7

> Username: arthaud  
> Created at: 2019-01-06 22:35:30 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451781560  

I confirm that the patch fixes the issue, thank you.

---

## Comment 8

> Username: igaztanaga  
> Created at: 2019-01-06 23:39:15 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451786292  

Many thanks for the report and help solving the issue. Fixed in commit:  
  
https://github.com/boostorg/container/commit/2b5197a5cb65a752b72296e441b2b211ebe8d2d2

---

## Comment 9

> Username: igaztanaga  
> Created at: 2019-01-07 00:02:23 UTC  
> Url: https://github.com/boostorg/container/issues/97#issuecomment-451788005  

There was  a typo in the committed patch, corrected with commit:  
  
https://github.com/boostorg/container/commit/80789255b9e143e2c23e171f63872e001bc1b5c5
