# #228 - boost/detail/iterator.hpp is deprecated. [Closed]

> Username: roccomoretti  
> Created at: 2020-10-13 00:38:06 UTC  
> Updated at: 2022-02-08 21:10:16 UTC  
> Closed at: 2022-02-08 21:10:16 UTC  
> Url: https://github.com/boostorg/graph/issues/228  

As of Boost 1.74, the boost/detail/iterator.hpp header is deprecated and may print a warning message (`#pragma message: This header is deprecated. Use <iterator> instead.`)  
  
The graph library directly uses the header in several locations:  
* https://github.com/boostorg/graph/blob/develop/include/boost/graph/adjacency_iterator.hpp#L13  
* https://github.com/boostorg/graph/blob/develop/include/boost/graph/incremental_components.hpp#L16  
* https://github.com/boostorg/graph/blob/develop/include/boost/detail/algorithm.hpp#L33  
   
This can results in a number of spurious compiler warning messages for people using the graph library.  
  
(Issue encountered compiling with GCC 5.2 on Linux with -std=c++11, using the version tagged `boost-1.74.0`)

---

## Comment 1

> Username: thebrandre  
> Created at: 2020-11-06 08:38:57 UTC  
> Updated at: 2020-11-06 08:43:21 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-722952621  

This issue still occurs in the current master branch for 1.75 when compiling with VS 2017.   
  
It seems to be fixed [by this commit on develop](https://github.com/boostorg/graph/commit/e4e12158e78157397254505f6cbae688e3e3174f). Can you please merge that into master for the 1.75 release?

---

## Comment 2

> Username: SpiritDogstar  
> Created at: 2020-12-31 19:26:41 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-753032111  

bump, running into this also.

---

## Comment 3

> Username: ghost  
> Created at: 2021-01-31 23:01:33 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-770467086  

Detailed message sample:  
```  
14>C:\local\boost_1_74_0\boost/range/result_iterator.hpp(20): message : This header is deprecated. Use <boost/range/iterator.hpp> instead.  
14>C:\local\boost_1_74_0\boost/range/const_reverse_iterator.hpp(20): message : This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.  
14>C:\local\boost_1_74_0\boost/range/reverse_result_iterator.hpp(20): message : This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.  
```

---

## Comment 4

> Username: roccomoretti  
> Created at: 2021-02-12 19:42:24 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-778411928  

It looks like the merge of PR #218 fixed this issue. It wasn't merged in time to hit Boost 1.75, but I'm guessing it will be included when Boost 1.76 comes out.

---

## Comment 5

> Username: bullestock  
> Created at: 2021-05-03 10:41:33 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-831178007  

Does not appear to be fixed in Boost 1.76.0:  
  
```  
In file included from /opt/boost/1.76.0/include/boost/math/tools/cxx03_warn.hpp:9,  
                 from /opt/boost/1.76.0/include/boost/math/constants/constants.hpp:11,  
                 from /opt/boost/1.76.0/include/boost/geometry/util/math.hpp:30,  
[...]  
/opt/boost/1.76.0/include/boost/detail/iterator.hpp:13:1: note: ‘#pragma message: This header is deprecated. Use <iterator> instead.’```

---

## Comment 6

> Username: ghost  
> Created at: 2021-05-03 15:41:44 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-831348908  

That's a pity. I was hoping, but now it looks like we have to wait a version or two more before a solution is implemented.

---

## Comment 7

> Username: wolframroesler  
> Created at: 2021-08-10 10:54:14 UTC  
> Updated at: 2022-01-26 13:32:46 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-895930518  

Can confirm the problem still exists in Boost 1.76.0. This in my source code:  
  
```cpp  
#include <boost/graph/adjacency_list.hpp>  
```  
  
produces the following message:  
  
```  
In file included from /usr/local/include/boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from /usr/local/include/boost/smart_ptr/detail/yield_k.hpp:23,  
                 from /usr/local/include/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from /usr/local/include/boost/smart_ptr/detail/spinlock.hpp:42,  
                 from /usr/local/include/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from /usr/local/include/boost/smart_ptr/shared_ptr.hpp:29,  
                 from /usr/local/include/boost/property_map/vector_property_map.hpp:14,  
                 from /usr/local/include/boost/property_map/property_map.hpp:602,  
                 from /usr/local/include/boost/graph/adjacency_list.hpp:27,  
                 from /home/wolfram/src/sdb/connector/cmdhdl/importbom.cpp:12:  
/usr/local/include/boost/detail/iterator.hpp:13:1: note: ‘#pragma message: This header is deprecated. Use <iterator> instead.’  
   13 | BOOST_HEADER_DEPRECATED("<iterator>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The compiler is gcc 10.3 on Ubuntu 21.04.  
  
EDIT: No longer occurs with Boost 1.78.0.

---

## Comment 8

> Username: TommyPec  
> Created at: 2022-01-19 21:48:51 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1016897790  

This bug is still there - on Boost  1.76.0. My humble suggestion is to not close a bug unless it's resolved.

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2022-02-07 23:41:44 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1032053246  

Could someone please test again on the latest version of Boost, and please be specific as to whether you are testing a packaged release or head of `master` or `develop`? According to the comments on the pull request, this looks like it should be in the latest packaged release. Thank you!

---

## Comment 10

> Username: phcerdan  
> Created at: 2022-02-08 08:56:53 UTC  
> Updated at: 2022-02-08 08:57:22 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1032364023  

I recently compiled with release `1.78.0` and didn't get the warnings.

---

## Comment 11

> Username: jakobandersen  
> Created at: 2022-02-08 11:18:21 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1032496697  

Compiling from the packaged sources I get the warnings with 1.76 but neither with 1.77 nor 1.78.

---

## Comment 12

> Username: thomastrapp  
> Created at: 2022-02-08 12:04:13 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1032534478  

I got the deprecation warnings with Boost 1.76. But I do not get these warnings with Boost 1.78.

---

## Comment 13

> Username: wolframroesler  
> Created at: 2022-02-08 19:35:22 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1032987314  

No longer occurs for me with Boost 1.78.0.

---

## Comment 14

> Username: jeremy-murphy  
> Created at: 2022-02-08 21:10:06 UTC  
> Url: https://github.com/boostorg/graph/issues/228#issuecomment-1033064764  

Great, thanks everyone, sounds conclusive.
