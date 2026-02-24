# #245 - Upgrading to 1.75.0 and hitting lots deprecated warnings for detail/iterator.hpp [Closed]

> Username: nicsav  
> Created at: 2021-03-04 18:06:16 UTC  
> Updated at: 2021-04-20 17:33:45 UTC  
> Closed at: 2021-04-20 17:33:45 UTC  
> Url: https://github.com/boostorg/graph/issues/245  

Hi all, I'm upgrading to 1.75.0 (from 1.59.0) and, even though I'm not directly including the offending file, I'm getting a lot of these warnings:  
  
In file included from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/detail/yield_k.hpp:23,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/detail/spinlock.hpp:42,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/smart_ptr/shared_ptr.hpp:29,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/property_map/vector_property_map.hpp:14,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/property_map/property_map.hpp:602,  
                 from /tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/graph/graphviz.hpp:19,  
                 from /home/my/code/some.cpp:21:  
/tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/detail/iterator.hpp:13:1: note: #pragma message: This header is deprecated. Use <iterator> instead.  
   13 | BOOST_HEADER_DEPRECATED("<iterator>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
  
It happens from various other boost headers (shared_pointer.hpp, depth_first_search.hpp, graph/adjacency_list.hpp, etc).  
  
Oddly enough it does not happen for all places where, say shared_pointer.hpp, is included from my code. So, I'm a bit stumped.  
  
I'm compiling with GCC9.1.0 in C++17 mode.  
  
Any help appreciated,  
Nick

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-04 18:27:15 UTC  
> Url: https://github.com/boostorg/graph/issues/245#issuecomment-790830548  

The deprecated `boost/detail/iterator.hpp` is not included by any SmartPtr code. Line 22 of `sp_thread_sleep.hpp` is https://github.com/boostorg/smart_ptr/blob/dc2a127369f3fb01e1f640072154291e7bec98f7/include/boost/smart_ptr/detail/sp_thread_sleep.hpp#L22, which actually includes `boost/config/pragma_message.hpp`. Something else must be including `boost/detail/iterator.hpp`.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-04 18:37:44 UTC  
> Url: https://github.com/boostorg/graph/issues/245#issuecomment-790838480  

On the current master branch, `boost/detail/iterator.hpp` is included here:  
```  
Inclusion report for <boost/detail/iterator.hpp> (in module core):  
  
    from graph:  
        <boost/detail/algorithm.hpp>  
        <boost/graph/adjacency_iterator.hpp>  
        <boost/graph/incremental_components.hpp>  
```  
(and nowhere on develop), so I'll move this issue to Graph.

---

## Comment 3

> Username: nicsav  
> Created at: 2021-03-04 21:06:59 UTC  
> Updated at: 2021-03-04 21:08:34 UTC  
> Url: https://github.com/boostorg/graph/issues/245#issuecomment-790941775  

Yeah, I turned on g++ -M and it showed that boost/graph/adjacency_list.hpp was included (prior to shared_ptr.hpp) and that resulted in:  
  
 ...  
 /calypto/tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/graph/adjacency_iterator.hpp \  
 /calypto/tools/install/boost/boost_1_75_0/rhel7/m64/include/boost/detail/iterator.hpp \  
  
being included.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-04-20 17:33:44 UTC  
> Url: https://github.com/boostorg/graph/issues/245#issuecomment-823469777  

This appears to now be fixed in master: apologies it didn't make 1.76, but will now be in 1.77.
