# #90 - UBSAN failures detected in preflight CI PR [Closed]

> Username: jeking3  
> Created at: 2018-11-13 13:15:17 UTC  
> Updated at: 2020-07-12 10:59:59 UTC  
> Closed at: 2020-07-12 10:59:59 UTC  
> Url: https://github.com/boostorg/container/issues/90  

Setup:  
```  
  gcc-8:    &gcc-8    { apt: { packages: [ "g++-8"           ], sources: [ "ubuntu-toolchain-r-test"   ] } }  
```  
  
Command:  
```  
UBSAN_OPTIONS=print_stacktrace=1 /home/travis/build/jeking3/boost-root/b2 . toolset=gcc-8 cxxstd=03,11,14,17,2a cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined define=BOOST_NO_STRESS_TEST=1 linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -j3  
```  
  
Output (first 2 failures):  
  
https://travis-ci.org/jeking3/container/jobs/454295437#L1090  
```  
gcc.compile.c++ ../../bin.v2/libs/container/test/tree_test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/tree_test.o  
gcc.link ../../bin.v2/libs/container/test/tree_test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/tree_test  
testing.capture-output ../../bin.v2/libs/container/test/tree_test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/tree_test.run  
====== BEGIN OUTPUT ======  
../../boost/intrusive/detail/hook_traits.hpp:61:10: runtime error: reference binding to null pointer of type 'struct slist_base_hook'  
    #0 0x47e9a5 in boost::intrusive::bhtraits_base<boost::intrusive::slist_base_hook<boost::intrusive::void_pointer<void*>, boost::intrusive::link_mode<(boost::intrusive::link_mode_type)0>, void>, boost::intrusive::slist_node<void*>*, boost::intrusive::dft_tag, 2u>::to_value_ptr(boost::intrusive::slist_node<void*>* const&) ../../boost/intrusive/detail/hook_traits.hpp:61  
    #1 0x466c93 in boost::intrusive::slist_iterator<boost::intrusive::bhtraits<boost::intrusive::slist_base_hook<boost::intrusive::void_pointer<void*>, boost::intrusive::link_mode<(boost::intrusive::link_mode_type)0>, void>, boost::intrusive::slist_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 2u>, false>::operator_arrow(boost::move_detail::bool_<false>) const ../../boost/intrusive/detail/slist_iterator.hpp:112  
    #2 0x4430c0 in boost::intrusive::slist_iterator<boost::intrusive::bhtraits<boost::intrusive::slist_base_hook<boost::intrusive::void_pointer<void*>, boost::intrusive::link_mode<(boost::intrusive::link_mode_type)0>, void>, boost::intrusive::slist_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 2u>, false>::operator->() const ../../boost/intrusive/detail/slist_iterator.hpp:104  
    #3 0x441f3c in boost::container::dtl::basic_multiallocation_chain<void*>::extract_data() ../../boost/container/detail/multiallocation_chain.hpp:174  
    #4 0x401553 in boost::container::dtl::fake_segment_manager::deallocate_many(boost::container::dtl::basic_multiallocation_chain<void*>&) ../../boost/container/detail/pool_common_alloc.hpp:52  
    #5 0x49d135 in boost::container::dtl::private_adaptive_node_pool_impl_common<boost::container::dtl::fake_segment_manager, 6u>::priv_clear(unsigned long, unsigned long, unsigned long) ../../boost/container/detail/adaptive_node_pool_impl.hpp:937  
    #6 0x497fdd in boost::container::dtl::private_adaptive_node_pool_impl_ct<boost::container::dtl::fake_segment_manager, 2ul, 32ul, 256ul, 1ul, 6u>::~private_adaptive_node_pool_impl_ct() ../../boost/container/detail/adaptive_node_pool_impl.hpp:1081  
    #7 0x48ff6e in boost::container::dtl::private_adaptive_node_pool<32ul, 256ul, 2ul, 1ul>::~private_adaptive_node_pool() ../../boost/container/detail/adaptive_node_pool.hpp:51  
    #8 0x4900f6 in boost::container::dtl::shared_adaptive_node_pool<32ul, 256ul, 2ul, 1ul>::~shared_adaptive_node_pool() ../../boost/container/detail/adaptive_node_pool.hpp:112  
    #9 0x7f72648991a8  (/lib/x86_64-linux-gnu/libc.so.6+0x3c1a8)  
    #10 0x7f72648991f4 in exit (/lib/x86_64-linux-gnu/libc.so.6+0x3c1f4)  
    #11 0x7f726487ef4b in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f4b)  
    #12 0x4010e8  (/home/travis/build/jeking3/boost-root/bin.v2/libs/container/test/tree_test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/tree_test+0x4010e8)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
https://travis-ci.org/jeking3/container/jobs/454295437#L1556  
```  
gcc.compile.c++ ../../bin.v2/libs/container/test/resource_adaptor_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/resource_adaptor_test.o  
gcc.link ../../bin.v2/libs/container/test/resource_adaptor_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/resource_adaptor_test  
testing.capture-output ../../bin.v2/libs/container/test/resource_adaptor_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/resource_adaptor_test.run  
====== BEGIN OUTPUT ======  
../../boost/container/pmr/resource_adaptor.hpp:175:16: runtime error: reference binding to misaligned address 0x7ffd4b1992b5 for type 'struct resource_adaptor_imp', which requires 8 byte alignment  
0x7ffd4b1992b5: note: pointer points here  
 92 19 4b fd 01 00 00  b5 92 19 4b fd 7f 00 00  b5 92 19 4b fd 7f 00 00  e0 92 19 4b fd 7f 00 00  b5  
             ^   
    #0 0x4077c2 in boost::container::pmr::resource_adaptor<propagation_test_allocator<char, 0u, false> >::resource_adaptor(boost::rv<propagation_test_allocator<char, 0u, false> >&) ../../boost/container/pmr/resource_adaptor.hpp:175  
    #1 0x4023b2 in test_rvalue_alloc_constructor() ../../libs/container/test/resource_adaptor_test.cpp:57  
    #2 0x404b7a in main ../../libs/container/test/resource_adaptor_test.cpp:182  
    #3 0x7f730bc92f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #4 0x401108  (/home/travis/build/jeking3/boost-root/bin.v2/libs/container/test/resource_adaptor_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/resource_adaptor_test+0x401108)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-13 21:20:48 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-438441332  

Let's see if the commit fixes the problem. Thanks for the report!

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-14 02:53:09 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-438519043  

I'll re-run the preflight CI with this change.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-14 12:52:07 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-438651366  

Still two lingering issues here (they appear 5 times each, once for each language level that was tested):  
  
https://travis-ci.org/jeking3/container/jobs/454804456#L1797  
https://travis-ci.org/jeking3/container/jobs/454804456#L2433

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-14 12:54:59 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-438652158  

@igaztanaga once we have these issues ironed out (or even before that) will you accept a pull request updating CI, license, and readme?  Here are the contents.  
  
You would also need to enable AppVeyor builds on this repository.  If you need help getting that set up, let me know.  
  
https://github.com/jeking3/container/pull/1

---

## Comment 5

> Username: igaztanaga  
> Created at: 2018-11-14 20:44:03 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-438811464  

I could not find any error in the code. However, I've found some reference to UBSAN false positives with -fvisibility=hidden:  
  
Example 1:  
https://bugzilla.redhat.com/show_bug.cgi?id=1513172  
  
Example 2:  
https://github.com/boostorg/thread/pull/236  
  
Example 3:  
https://github.com/boostorg/thread/pull/237  
  
Looks like UBSAN tests should be executed with -fvisibility=global, as suggested by Peter Dimov in Example 2.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2020-07-12 10:59:59 UTC  
> Url: https://github.com/boostorg/container/issues/90#issuecomment-657206344  

Closing the issue due to inactivity.
