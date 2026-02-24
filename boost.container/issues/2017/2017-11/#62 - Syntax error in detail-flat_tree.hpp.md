# #62 - Syntax error in detail/flat_tree.hpp? [Closed]

> Username: pdimov  
> Created at: 2017-11-02 12:14:13 UTC  
> Updated at: 2017-11-02 14:03:59 UTC  
> Closed at: 2017-11-02 14:03:59 UTC  
> Url: https://github.com/boostorg/container/issues/62  

```  
gcc.compile.c++ bin.v2/libs/container/test/scoped_allocator_usage_test.test/gcc-gnu-4.8/debug/cxxstd-11-iso/threadapi-pthread/scoped_allocator_usage_test.o  
In file included from ./boost/container/flat_map.hpp:29:0,  
                 from libs/container/test/scoped_allocator_usage_test.cpp:20:  
./boost/container/detail/flat_tree.hpp:459:68: error: expected unqualified-id before ‘,’ token  
       BOOST_INTRUSIVE_HAS_TYPE(boost::container::container_detail::, container_type, stored_allocator_type);  
                                                                    ^  
```  
  
From https://travis-ci.org/boostorg/boost/jobs/295693904 and https://ci.appveyor.com/project/boostorg/boost/build/1.0.4464-master/job/mhcujct9n5lj4fdg.  
  
I can't reproduce this locally, so it might have been caused by a temporarily broken Interprocess.  
  
I'm enabling some minimal Travis here, to get some CI.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-11-02 13:57:26 UTC  
> Url: https://github.com/boostorg/container/issues/62#issuecomment-341428777  

Looks like the Intrusive library is not updated for these tests. Boost.Container is using a macro from Boost.Intrusive that is not present in the tested version. Maybe the superproject is not updated?

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-02 14:03:59 UTC  
> Url: https://github.com/boostorg/container/issues/62#issuecomment-341430931  

Yes, that's it. You've updated both intrusive and container at the same time, and the bot has updated container first in https://github.com/boostorg/boost/commit/69685c28acd193d7a041e5570bdf6151dd2962c3, then intrusive in https://github.com/boostorg/boost/commit/89050221222049a1dc00f239090f43f53f94ccf1. False alarm.
