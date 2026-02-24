# #124 Fix use-after-free of s_frk_state()::the_inst [Closed]

> Username: avikivity  
> Created at: 2018-01-01 14:09:22 UTC  
> Updated at: 2018-02-20 19:28:41 UTC  
> Closed at: 2018-02-20 19:28:41 UTC  
> Url: https://github.com/boostorg/test/pull/124  

s_frk_state()::the_inst is a statically scoped object that is  
referred to by another statically scoped object, s_log_impl()::the_inst.  
Since the ordering of destruction among statically scoped objects that  
are defined in different translation units is not defined, we can observe  
s_frk_state()::the_inst being destroyed before s_log_impl()::the_inst,  
leading to a use-after-free when the latter is destroyed.  
  
"Fix" by intentionally leaking s_frk_state::the_inst.  We also need to  
initialize its member m_master_test_suite, which can be initialized to  
a random value when allocating from the heap, leading a later test  
of that member to fail.  
  
Addresses issue 13371.

---

## Comment 1

> Username: avikivity  
> Created_at: 2018-01-13 19:38:40 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-357460430  

A gentle ping.

---

## Comment 2

> Username: avikivity  
> Created_at: 2018-01-21 18:08:58 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359267810  

Ping^2

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2018-01-21 18:36:11 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359269762  

Hi,   
I already replied to you on the boost ML. Haven't you received it? I had the same fix for the master test suite for another bug visible on Cygwin, however the fix concerning the leak of the framework state in all cases is not an acceptable solution.   
Do you know in what circumstances the state is accessed after it being destroyed?

---

## Comment 4

> Username: avikivity  
> Created_at: 2018-01-22 08:17:47 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359352839  

>I already replied to you on the boost ML. Haven't you received it?   
  
No, I did not.  
  
>I had the same fix for the master test suite for another bug visible on Cygwin, however the fix concerning the leak of the framework state in all cases is not an acceptable solution.  
  
I agree that it's a very bad solution, but there is such a tangle of singletons that I could not come up with a better one. Personally I dislike singletons and avoid them as much as I can. Here, perhaps the best solution is to have exactly one singleton and fold all the rest into it as data members, but my understanding of the code is not sufficient to implement it.  
  
>Do you know in what circumstances the state is accessed after it being destroyed?  
  
I see it when `--log_sink` is used to redirect the output to a file, when building on Fedora 27. It may not happen all times, since the compiler may happen to order the destructors in a way that works.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2018-01-22 08:32:52 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359355719  

That is unfortunate that you haven't see the reply on the ML, I have to say that the ML does not work as it should.  
  
Concerning the singleton: that is indeed the case. I think the problem comes from   
  
``  
unit_test_log_impl& s_log_impl() { static unit_test_log_impl the_inst; return the_inst; }  
``  
which is currently a singleton as well. We should be able to make it a member of ``unit_test_log_t`` instead, without any effort (``unit_test_log_t`` is already a singleton through ``unit_test_log``).  
  
It would help if you rebase your PR on develop and modify it according to what I suggested above, and tell me if that fixes your issue. If not, then the lifetime relationship between ``unit_test_log`` and the framework state should also be fixed.  
  
Thanks.

---

## Comment 6

> Username: avikivity  
> Created_at: 2018-01-22 11:12:17 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359393236  

> Concerning the singleton: that is indeed the case. I think the problem comes from unit_test_log_impl& s_log_impl() { static unit_test_log_impl the_inst; return the_inst; }  
which is currently a singleton as well. We should be able to make it a member of unit_test_log_t instead, without any effort (unit_test_log_t is already a singleton through unit_test_log).  
  
No, as stated in the description, the problem is lack of ordering in the destructors of `s_frk_state()::the_inst` vs `s_log_impl()::the_inst`.  
  
IMO the only real solution is to make sure everything is a member of `framework::state` so there is just one singleton (avoiding any reordering problems), but I have no idea what #include hell this will create.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2018-01-22 23:20:39 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-359608027  

Hi again,  
Thank you for your insights. There is indeed a lot of global objects, and we cannot make the ``framework::state`` the only global singleton. In fact, this would not work when we run the framework several times in a row.  
  
I made a blind fix of your issue though. Would you be able to test this on the branch ``topic/13371-logstream-use-after-destroy`` ? Thanks!

---

## Comment 8

> Username: avikivity  
> Created_at: 2018-01-24 15:25:55 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360170158  

Will move the conversation there.

---

## Comment 9

> Username: avikivity  
> Created_at: 2018-01-24 15:26:57 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360170473  

Actually, there is no there. I backported the patches to boost-1.64.0 and tested, still broken:  
  
```  
./build/debug/tests/types_test_g --log_sink=/tmp/log  
WARNING: debug mode. Not for benchmarking or production  
WARN  2018-01-24 17:24:14,883 seastar - Seastar compiled with default allocator, heap profiler not supported  
WARN  2018-01-24 17:24:14,893 seastar - Seastar compiled with default allocator, heap profiler not supported  
WARN  2018-01-24 17:24:14,918 [shard 0] seastar - Unable to set SCHED_FIFO scheduling policy for timer thread; latency impact possible. Try adding CAP_SYS_NICE  
WARN  2018-01-24 17:24:15,365 [shard 0] org.apache.cassandra.db.marshal.TimestampType - Changing from DateType to TimestampType is allowed, but be wary that they sort differently for pre-unix-epoch timestamps (negative timestamp values) and thus this change will corrupt your data if you have such negative timestamp. So unless you know that you don't have *any* pre-unix-epoch timestamp you should change back to DateType  
WARN  2018-01-24 17:24:15,366 [shard 0] org.apache.cassandra.db.marshal.DateType - Changing from TimestampType to DateType is allowed, but be wary that they sort differently for pre-unix-epoch timestamps (negative timestamp values) and thus this change will corrupt your data if you have such negative timestamp. There is no reason to switch from DateType to TimestampType except if you were using DateType in the first place and switched to TimestampType by mistake.  
  
*** No errors detected  
=================================================================  
==27429==ERROR: AddressSanitizer: heap-use-after-free on address 0x616000001fc8 at pc 0x00000496a51e bp 0x7fff63d1e520 sp 0x7fff63d1e510  
READ of size 8 at 0x616000001fc8 thread T0  
    #0 0x496a51d in std::ios_base::width(long) /usr/include/c++/7/bits/ios_base.h:723  
    #1 0xaae55e6 in boost::io::ios_base_all_saver::restore() /usr/include/boost/io/ios_state.hpp:340  
    #2 0xaae54ec in boost::io::ios_base_all_saver::~ios_base_all_saver() /usr/include/boost/io/ios_state.hpp:336  
    #3 0xab4e822 in void boost::checked_delete<boost::io::ios_base_all_saver>(boost::io::ios_base_all_saver*) /usr/include/boost/core/checked_delete.hpp:34  
    #4 0xabe1dfb in boost::detail::sp_counted_impl_p<boost::io::ios_base_all_saver>::dispose() /usr/include/boost/smart_ptr/detail/sp_counted_impl.hpp:78  
    #5 0x74bf9a in boost::detail::sp_counted_base::release() /usr/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:109  
    #6 0x74c172 in boost::detail::shared_count::~shared_count() /usr/include/boost/smart_ptr/detail/shared_count.hpp:419  
    #7 0xaae6378 in boost::shared_ptr<boost::io::ios_base_all_saver>::~shared_ptr() /usr/include/boost/smart_ptr/shared_ptr.hpp:337  
    #8 0xaa52d94 in ~unit_test_log_data_helper_impl /usr/include/boost/test/impl/unit_test_log.ipp:87  
    #9 0xaab9542 in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl> /usr/include/c++/7/bits/stl_construct.h:98  
    #10 0xaab43e9 in __destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*> /usr/include/c++/7/bits/stl_construct.h:108  
    #11 0xaaa98bf in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*> /usr/include/c++/7/bits/stl_construct.h:137  
    #12 0xaa95723 in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*, boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl> /usr/include/c++/7/bits/stl_construct.h:206  
    #13 0xaa89940 in ~vector /usr/include/c++/7/bits/stl_vector.h:434  
    #14 0xaa53d36 in ~unit_test_log_impl /usr/include/boost/test/impl/unit_test_log.ipp:122  
    #15 0x7fedf3f18b57 in __run_exit_handlers (/lib64/libc.so.6+0x3bb57)  
    #16 0x7fedf3f18ba9 in exit (/lib64/libc.so.6+0x3bba9)  
    #17 0x7fedf3efe010 in __libc_start_main (/lib64/libc.so.6+0x21010)  
    #18 0x420f59 in _start (/home/avi/scylla-maint/build/debug/tests/types_test_g+0x420f59)  
  
0x616000001fc8 is located 328 bytes inside of 576-byte region [0x616000001e80,0x6160000020c0)  
freed by thread T0 here:  
    #0 0x7fedf9bde6d8 in operator delete(void*, unsigned long) (/lib64/libasan.so.4+0xe16d8)  
    #1 0xabde634 in boost::detail::sp_counted_impl_pd<boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >::~sp_counted_impl_pd() /usr/include/boost/smart_ptr/detail/sp_counted_impl.hpp:127  
    #2 0xc5b5f5 in boost::detail::sp_counted_base::destroy() /usr/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:89  
    #3 0x74c0ba in boost::detail::sp_counted_base::weak_release() /usr/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:123  
    #4 0x74bfc9 in boost::detail::sp_counted_base::release() /usr/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:110  
    #5 0x74c172 in boost::detail::shared_count::~shared_count() /usr/include/boost/smart_ptr/detail/shared_count.hpp:419  
    #6 0xaac6122 in boost::shared_ptr<boost::unit_test::runtime_config::stream_holder::callback_cleaner>::~shared_ptr() /usr/include/boost/smart_ptr/shared_ptr.hpp:337  
    #7 0xaad76a4 in boost::unit_test::runtime_config::stream_holder::~stream_holder() /usr/include/boost/test/unit_test_parameters.hpp:99  
    #8 0xabc0fda in std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>::~pair() /usr/include/c++/7/bits/stl_pair.h:198  
    #9 0xabc1042 in void __gnu_cxx::new_allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::destroy<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >(std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>*) /usr/include/c++/7/ext/new_allocator.h:140  
    #10 0xabb3c1f in void std::allocator_traits<std::allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > > >::destroy<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >(std::allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >&, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>*) /usr/include/c++/7/bits/alloc_traits.h:487  
    #11 0xab9ba48 in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_destroy_node(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:650  
    #12 0xab717b1 in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_drop_node(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:658  
    #13 0xab42ced in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_erase(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:1858  
    #14 0xab45814 in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::clear() /usr/include/c++/7/bits/stl_tree.h:1171  
    #15 0xab11c92 in std::map<boost::unit_test::output_format, boost::unit_test::runtime_config::stream_holder, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::clear() /usr/include/c++/7/bits/stl_map.h:1113  
    #16 0xaa2564e in boost::unit_test::framework::impl::shutdown_loggers() /usr/include/boost/test/impl/framework.ipp:844  
    #17 0xaa2c5ed in boost::unit_test::framework::shutdown() /usr/include/boost/test/impl/framework.ipp:1129  
    #18 0xaa6344f in boost::unit_test::unit_test_main(bool (*)(), int, char**) /usr/include/boost/test/impl/unit_test_main.ipp:258  
    #19 0xaa891dd in main seastar/tests/test-utils.cc:84  
    #20 0x7fedf3efe009 in __libc_start_main (/lib64/libc.so.6+0x21009)  
  
previously allocated by thread T0 here:  
    #0 0x7fedf9bdd158 in operator new(unsigned long) (/lib64/libasan.so.4+0xe0158)  
    #1 0xab61868 in boost::detail::shared_count::shared_count<boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >(boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_inplace_tag<boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >) /usr/include/boost/smart_ptr/detail/shared_count.hpp:206  
    #2 0xab375e5 in boost::shared_ptr<boost::unit_test::runtime_config::stream_holder::callback_cleaner>::shared_ptr<boost::unit_test::runtime_config::stream_holder::callback_cleaner, boost::detail::sp_inplace_tag<boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> > >(boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_inplace_tag<boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >) /usr/include/boost/smart_ptr/shared_ptr.hpp:372  
    #3 0xab027fe in boost::detail::sp_if_not_array<boost::unit_test::runtime_config::stream_holder::callback_cleaner>::type boost::make_shared<boost::unit_test::runtime_config::stream_holder::callback_cleaner, boost::function<void ()> const&>(boost::function<void ()> const&) /usr/include/boost/smart_ptr/make_shared_object.hpp:250  
    #4 0xaac6490 in boost::unit_test::runtime_config::stream_holder::setup(boost::unit_test::basic_cstring<char const> const&, boost::function<void ()> const&) /usr/include/boost/test/unit_test_parameters.hpp:118  
    #5 0xaa264b0 in boost::unit_test::framework::impl::setup_loggers() /usr/include/boost/test/impl/framework.ipp:867  
    #6 0xaa2aae4 in boost::unit_test::framework::init(bool (*)(), int, char**) /usr/include/boost/test/impl/framework.ipp:1043  
    #7 0xaa62a40 in boost::unit_test::unit_test_main(bool (*)(), int, char**) /usr/include/boost/test/impl/unit_test_main.ipp:189  
    #8 0xaa891dd in main seastar/tests/test-utils.cc:84  
    #9 0x7fedf3efe009 in __libc_start_main (/lib64/libc.so.6+0x21009)  
  
SUMMARY: AddressSanitizer: heap-use-after-free /usr/include/c++/7/bits/ios_base.h:723 in std::ios_base::width(long)  
Shadow bytes around the buggy address:  
  0x0c2c7fff83a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c2c7fff83b0: 00 00 00 00 06 fa fa fa fa fa fa fa fa fa fa fa  
  0x0c2c7fff83c0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c2c7fff83d0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x0c2c7fff83e0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
=>0x0c2c7fff83f0: fd fd fd fd fd fd fd fd fd[fd]fd fd fd fd fd fd  
  0x0c2c7fff8400: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x0c2c7fff8410: fd fd fd fd fd fd fd fd fa fa fa fa fa fa fa fa  
  0x0c2c7fff8420: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c2c7fff8430: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x0c2c7fff8440: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
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
==27429==ABORTING  
```

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2018-01-24 15:38:25 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360174264  

The fix in the branch consists in replacing the current junit stream (file) by an stream to std::cout. The ``boost::unit_test::framework::impl::shutdown_loggers`` clears the state internal ``std::map``, that should during the dtor of the elements replace the stream of the junit to something else (``std::cout``). So I fail to understand why the ``ios_base_all_saver`` is still accessing the stream: the reference to it should have been moved to ``std::cout``.  
  
May I ask the code, OS, compiler and command line that you are running? I will try to reproduce on my end.

---

## Comment 11

> Username: avikivity  
> Created_at: 2018-01-24 15:56:19 UTC  
> Updated_at: 2018-01-24 15:56:55 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360180198  

Fedora-27, and I'm running the seastar test suite in debug mode.  
  
https://github.com/scylladb/seastar  
  
```  
git clone --recursive https://github.com/scylladb/seastar.git  
cd seastar  
sudo ./install-dependencies.sh  
./configure.py --tests-deuginfo 1  
ninja-build debug  
./test.py --mode debug  
```

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2018-01-24 15:58:37 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360181024  

Thanks, installing the VM ...

---

## Comment 13

> Username: avikivity  
> Created_at: 2018-01-24 16:10:00 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360184818  

Make sure to give it plenty of RAM. We also have a docker image with all the dependencies pre-installed, if that's any help.

---

## Comment 14

> Username: raffienficiaud  
> Created_at: 2018-01-24 16:12:54 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360185794  

Thanks, I am on OSX and docker does not help.

---

## Comment 15

> Username: avikivity  
> Created_at: 2018-01-24 16:15:57 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-360186823  

Oh, my condolences.

---

## Comment 16

> Username: raffienficiaud  
> Created_at: 2018-01-27 19:02:48 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361007099  

Fedora is greedy on resources ... so this is what I got  
  
```  
[raffi@sow-fedora27-tests seastar]$ history | tail  
   22  cd seastar/  
   23  sudo ./install-dependencies.sh  
   24  ./configure.py --tests-debuginfo 1  
   25  ninja-build debug  
   26  ./test.py --mode debug  
```  
  
and I have things like  
```  
FAILED: build/debug/tests/distributed_test --smp=2  
  with error code -6                                 
=== stdout START ===  
=== stdout END ===  
=== stderr START ===  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  insufficient processing units  
Aborting.  
Backtrace:  
  0x0000000000a860c9  
  0x0000000000753b23  
  0x0000000000753c1b  
  0x00000000007c869d  
  0x0000000000869b4c  
  0x0000000000869beb  
  /lib64/libpthread.so.0+0x0000000000012a6f  
  /lib64/libc.so.6+0x000000000003769a  
  /lib64/libc.so.6+0x00000000000393b0  
  /lib64/libstdc++.so.6+0x0000000000092024  
  /lib64/libstdc++.so.6+0x000000000008fc15  
  /lib64/libstdc++.so.6+0x000000000008fc60  
  /lib64/libstdc++.so.6+0x000000000008fea3  
  0x0000000000dd6a7a  
  0x00000000007cc7b5  
  0x0000000000eebd01  
  0x0000000000ee8fb5  
  0x0000000000ee992f  
  0x000000000041dc43  
  /lib64/libc.so.6+0x0000000000021039  
  0x00000000004125b9  
  
=== stderr END ===  
```  
  
how do I hook configure the build to have another boost.test ? does the configuration script understands environment variables and compiler/linker flags? Would you please provide me yours?

---

## Comment 17

> Username: raffienficiaud  
> Created_at: 2018-01-27 20:02:22 UTC  
> Updated_at: 2018-01-27 20:06:04 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361011131  

So ... I am trying the different options of configure. In the meanwhile, I just noticed that you used the ``--log_sink`` option, and this is what you are executing:  
  
https://github.com/boostorg/test/blob/topic/13371-logstream-use-after-destroy/include/boost/test/impl/framework.ipp#L941  
  
Would you just try again with the [``--logger``](http://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/logger.html) option instead?  
  
The reason why I am asking you this is that apparently this code does not compile well with boost1.66 (which is the one I have).

---

## Comment 18

> Username: raffienficiaud  
> Created_at: 2018-01-28 12:57:11 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361061041  

```  
[raffi@sow-fedora27-tests seastar]$ ./build/debug/tests/json_formatter_test --logger=JUNIT,all,/tmp/log                
WARNING: debug mode. Not for benchmarking or production                                                                
WARN  2018-01-28 13:50:02,849 seastar - Seastar compiled with default allocator, heap profiler not supported           
WARN  2018-01-28 13:50:02,862 [shard 0] seastar - Unable to set SCHED_FIFO scheduling policy for timer thread; latenc  
y impact possible. Try adding CAP_SYS_NICE                                                                             
                                                                                                                       
*** No errors detected                                                                                                 
```  
and  
```  
[raffi@sow-fedora27-tests seastar]$ ./build/debug/tests/json_formatter_test --log_sink=/tmp/log                        
WARNING: debug mode. Not for benchmarking or production                                                                
WARN  2018-01-28 13:50:43,220 seastar - Seastar compiled with default allocator, heap profiler not supported           
WARN  2018-01-28 13:50:43,233 [shard 0] seastar - Unable to set SCHED_FIFO scheduling policy for timer thread; latenc  
y impact possible. Try adding CAP_SYS_NICE                                                                             
                                                                                                                       
*** No errors detected                                                                                                 
=================================================================                                                      
==5787==ERROR: AddressSanitizer: heap-use-after-free on address 0x6160000010c8 at pc 0x00000117253c bp 0x7ffdf8781140  
 sp 0x7ffdf8781130                                                                                                     
READ of size 8 at 0x6160000010c8 thread T0                                                                             
    #0 0x117253b in std::ios_base::width(long) /usr/include/c++/7/bits/ios_base.h:723                                  
    #1 0x1199b56 in boost::io::ios_base_all_saver::restore() /home/raffi/usr/local/include/boost/io/ios_state.hpp:340  
    #2 0x1199a5c in boost::io::ios_base_all_saver::~ios_base_all_saver() /home/raffi/usr/local/include/boost/io/ios_state.hpp:336                                                                                                           
    #3 0x120cd70 in void boost::checked_delete<boost::io::ios_base_all_saver>(boost::io::ios_base_all_saver*) /home/raffi/usr/local/include/boost/core/checked_delete.hpp:34                                                                
    #4 0x12a88b5 in boost::detail::sp_counted_impl_p<boost::io::ios_base_all_saver>::dispose() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_impl.hpp:78                                                                  
    #5 0x41b9a6 in boost::detail::sp_counted_base::release() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:109                                                                                        
    #6 0x41bc00 in boost::detail::shared_count::~shared_count() /home/raffi/usr/local/include/boost/smart_ptr/detail/shared_count.hpp:419                                                                                                   
    #7 0x119a91c in boost::shared_ptr<boost::io::ios_base_all_saver>::~shared_ptr() /home/raffi/usr/local/include/boost/smart_ptr/shared_ptr.hpp:337                                                                                        
    #8 0x110439a in ~unit_test_log_data_helper_impl /home/raffi/usr/local/include/boost/test/impl/unit_test_log.ipp:87                                                                                                                      
    #9 0x1169a86 in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl> /usr/include/c++/7/bits/stl_construct.h:98                                                                                            
    #10 0x11634fd in __destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*> /usr/include/c++/7/bits/stl_construct.h:108                                                                                        
    #11 0x11588bd in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*> /usr/include/c++/7/bits/stl_construct.h:137                                                                                         
    #12 0x1143d63 in _Destroy<boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl*, boost::unit_test::(anonymous namespace)::unit_test_log_data_helper_impl> /usr/include/c++/7/bits/stl_construct.h:206                
    #13 0x1137172 in ~vector /usr/include/c++/7/bits/stl_vector.h:434                                                  
    #14 0x110543a in ~unit_test_log_impl /home/raffi/usr/local/include/boost/test/impl/unit_test_log.ipp:122           
    #15 0x7f0f00404b87 in __run_exit_handlers (/lib64/libc.so.6+0x3bb87)                                               
    #16 0x7f0f00404bd9 in exit (/lib64/libc.so.6+0x3bbd9)                                                              
    #17 0x7f0f003ea040 in __libc_start_main (/lib64/libc.so.6+0x21040)                                                 
    #18 0x414ec9 in _start (/home/raffi/seastar/build/debug/tests/json_formatter_test+0x414ec9)                        
                                                                                                                       
0x6160000010c8 is located 328 bytes inside of 576-byte region [0x616000000f80,0x6160000011c0)                          
freed by thread T0 here:                                                                                               
    #0 0x7f0f053116d8 in operator delete(void*, unsigned long) (/lib64/libasan.so.4+0xe16d8)                           
    #1 0x12a500e in boost::detail::sp_counted_impl_pd<boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >::~sp_counted_impl_pd() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_impl.hpp:127                                  
    #2 0xc70529 in boost::detail::sp_counted_base::destroy() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:89                                                                                         
    #3 0x41bac6 in boost::detail::sp_counted_base::weak_release() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:123                                                                                   
    #4 0x41b9d5 in boost::detail::sp_counted_base::release() /home/raffi/usr/local/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:110                                                                                        
    #5 0x41bc00 in boost::detail::shared_count::~shared_count() /home/raffi/usr/local/include/boost/smart_ptr/detail/shared_count.hpp:419                                                                                                   
    #6 0x1178898 in boost::shared_ptr<boost::unit_test::runtime_config::stream_holder::callback_cleaner>::~shared_ptr() /home/raffi/usr/local/include/boost/smart_ptr/shared_ptr.hpp:337                                                    
    #7 0x118ba82 in boost::unit_test::runtime_config::stream_holder::~stream_holder() /home/raffi/usr/local/include/boost/test/unit_test_parameters.hpp:99                                                                                  
    #8 0x128701a in std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>::~pair() /usr/include/c++/7/bits/stl_pair.h:198                                                                       
    #9 0x1287082 in void __gnu_cxx::new_allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::destroy<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >(std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>*) /usr/include/c++/7/ext/new_allocator.h:140                                                
    #10 0x1279505 in void std::allocator_traits<std::allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > > >::destroy<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >(std::allocator<std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >&, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>*) /usr/include/c++/7/bits/alloc_traits.h:487            
    #11 0x125ec4c in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_destroy_node(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:650                                                                                   
    #12 0x1230bed in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_drop_node(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:658                                                                                      
    #13 0x11fe8ed in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::_M_erase(std::_Rb_tree_node<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >*) /usr/include/c++/7/bits/stl_tree.h:1858                                                                                         
    #14 0x12018fa in std::_Rb_tree<boost::unit_test::output_format, std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder>, std::_Select1st<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> >, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::clear() /usr/include/c++/7/bits/stl_tree.h:1171                                                                                              
    #15 0x11cb1da in std::map<boost::unit_test::output_format, boost::unit_test::runtime_config::stream_holder, std::less<boost::unit_test::output_format>, std::allocator<std::pair<boost::unit_test::output_format const, boost::unit_test::runtime_config::stream_holder> > >::clear() /usr/include/c++/7/bits/stl_map.h:1113                                 
    #16 0x10d3ff5 in boost::unit_test::framework::impl::shutdown_loggers() /home/raffi/usr/local/include/boost/test/impl/framework.ipp:915                                                                                                  
    #17 0x10dad0b in boost::unit_test::framework::shutdown() /home/raffi/usr/local/include/boost/test/impl/framework.ipp:1201                                                                                                               
    #18 0x1114c15 in boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/raffi/usr/local/include/boost/test/impl/unit_test_main.ipp:258                                                                                         
    #19 0x1136a74 in main tests/test-utils.cc:84                                                                       
    #20 0x7f0f003ea039 in __libc_start_main (/lib64/libc.so.6+0x21039)                                                 
                                                                                                                       
previously allocated by thread T0 here:                                                                                
    #0 0x7f0f05310158 in operator new(unsigned long) (/lib64/libasan.so.4+0xe0158)                                     
    #1 0x1220908 in boost::detail::shared_count::shared_count<boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >(boost::unit_test::runtime_config::stream_holder::callback_cleaner*, boost::detail::sp_inplace_tag<boost::detail::sp_ms_deleter<boost::unit_test::runtime_config::stream_holder::callback_cleaner> >) /home/raffi/usr/local/include/boost/smart_ptr/detail/shared_count.hpp:206                                                                                         
  
// etc etc  
```  
  
This seem to confirm my previous post. Would be good if you try on your end.

---

## Comment 19

> Username: avikivity  
> Created_at: 2018-01-28 16:08:49 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361073973  

I see exactly the same crash with  
  
    ./build/debug/tests/alloc_test_g --logger=JUNIT,all,/tmp/log

---

## Comment 20

> Username: raffienficiaud  
> Created_at: 2018-01-28 16:13:58 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361074331  

I cannot reproduce this one. I have ``./build/debug/tests/alloc_test`` only and when I run it, I have this:  
```  
SUMMARY: AddressSanitizer: heap-buffer-overflow /home/raffi/seastar/core/reactor.hh:1162 in submit_to<test_live_objec  
ts_counter_with_cross_cpu_free::run_test_case()::<lambda()> >                                                          
```  
The error is not the same as for the example I mentioned.

---

## Comment 21

> Username: avikivity  
> Created_at: 2018-01-28 16:14:21 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361074358  

With the patched boost (my patch, with the leak), both command lines succeed.

---

## Comment 22

> Username: raffienficiaud  
> Created_at: 2018-01-28 16:15:32 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361074440  

I am surprised that your patch does not report a memory leak.

---

## Comment 23

> Username: avikivity  
> Created_at: 2018-01-28 16:23:54 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361075074  

Can you try  
  
    ./build/debug/tests/alloc_test --log_sink=/tmp/log -- --smp 2  
  
I think your VM is misreporting the number of processors which confuses seastar.  
  
`build/debug/tests/alloc_test_g` (note _g) is the same thing, just built with debug symbols.  To build it, run `ninja build/debug/tests/alloc_test_g`. It doesn't make any difference to the test, except that you get clear backtraces when it fails.

---

## Comment 24

> Username: raffienficiaud  
> Created_at: 2018-01-28 16:29:07 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361075447  

After building how you mentioned, I have this:  
```  
[raffi@sow-fedora27-tests seastar]$ ./build/debug/tests/alloc_test --log_sink=/tmp/log -- --smp 2                      
WARNING: debug mode. Not for benchmarking or production                                                                
terminate called after throwing an instance of 'std::runtime_error'                                                    
  what():  insufficient processing units                                                                               
Aborting.                                                                                                              
Backtrace:                                                                                                             
  0x000000000079f8db                                                                                                   
  0x0000000000468405                                                                                                   
  0x00000000004684fd                                                                                                   
  0x00000000004dcf1b                                                                                                   
  0x000000000057d1dc                                                                                                   
  0x000000000057d27b                                                                                                   
  /lib64/libpthread.so.0+0x0000000000012a6f                                                                            
  /lib64/libc.so.6+0x000000000003769a                                                                                  
  /lib64/libc.so.6+0x00000000000393b0                                                                                  
  /lib64/libstdc++.so.6+0x0000000000092024                                                                             
  /lib64/libstdc++.so.6+0x000000000008fc15                                                                             
  /lib64/libstdc++.so.6+0x000000000008eb18                                                                             
  /lib64/libstdc++.so.6+0x000000000008f507                                                                             
  /lib64/libgcc_s.so.1+0x000000000000fee2                                                                              
  /lib64/libgcc_s.so.1+0x000000000001070d                                                                              
  0x00000000010c7e08                                                                                                   
  0x00000000010d0de2                                                                                                   
  0x00000000007bf3c9                                                                                                   
  0x0000000000b29656                                                                                                   
  /lib64/libpthread.so.0+0x0000000000007608                                                                            
  /lib64/libc.so.6+0x0000000000119e6e                                                                                  
Aborted (core dumped)                                                                                                  
```  
  
So maybe before going further, I should ask:  
  
* what was your line for the ``configure`` command for using your compiled version of Boost?  
* for boost itself, I just cloned the tag 1.64.0 and the replaced boost.test with the branch of the fix, then built with ``./b2 -j4 --prefix=/home/raffi/usr/local/ install``

---

## Comment 25

> Username: avikivity  
> Created_at: 2018-01-28 17:02:11 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361077960  

I just use `./configure.py`, and boost from Fedora's packages.  
  
You can try to work around the problem with  
  
     dnf remove 'hwloc*'  
  
and re-run configure. `hwloc` is a library that queries the machine's topology, and evidently is not working on your hypervisor (which hypervisor is that, btw?)

---

## Comment 26

> Username: raffienficiaud  
> Created_at: 2018-01-28 17:22:50 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361079426  

I use VMware on ESXi6. I actually pass the library and header location of my own compiled boost with this:  
  
```  
[raffi@sow-fedora27-tests seastar]$ ./configure.py --cflags="-I/home/raffi/usr/local/include -L/home/raffi/usr/local/lib -Wl,-rpath=/home/raffi/usr/local/lib" --tests-debuginfo=1  
```  
  
After recompiling without this ``hwloc`` library, I have this:  
```  
[raffi@sow-fedora27-tests seastar]$ ./build/debug/tests/alloc_test_g  -- --smp 2                               
Running 3 test cases...                                                                                        
WARNING: debug mode. Not for benchmarking or production                                                        
WARN  2018-01-28 18:21:47,061 seastar - Seastar compiled with default allocator, heap profiler not supported   
alloc_test_g: core/posix.hh:460: void seastar::pin_this_thread(unsigned int): Assertion `r == 0' failed.       
Aborting.                                                                                                      
Backtrace:                                                                                                     
  0x000000000079f43d                                                                                           
  0x0000000000467fa5                                                                                           
  0x000000000046809d                                                                                           
  0x00000000004dca7d                                                                                           
  0x000000000057cd3e                                                                                           
  0x000000000057cddd                                                                                           
  /lib64/libpthread.so.0+0x0000000000012a6f                                                                    
  /lib64/libc.so.6+0x000000000003769a                                                                          
  /lib64/libc.so.6+0x00000000000393b0                                                                          
  /lib64/libc.so.6+0x000000000002f929                                                                          
  /lib64/libc.so.6+0x000000000002f9a1                                                                          
  0x000000000078fff2                                                                                           
  0x00000000004dc4ad                                                                                           
  0x00000000004dd85b                                                                                           
  0x00000000005d261f                                                                                           
  0x00000000007bef2b                                                                                           
  0x0000000000b291b8                                                                                           
  /lib64/libpthread.so.0+0x0000000000007608                                                                    
  /lib64/libc.so.6+0x0000000000119e6e                                                                          
Aborted (core dumped)                                                                                          
```

---

## Comment 27

> Username: avikivity  
> Created_at: 2018-01-28 17:33:00 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361080184  

Did you start the VM with just one vcpu? Either start it with two (or more) vcpus, or use `--smp 1`.  
  
(`--smp` should not be needed any more, in the absence of hwloc seastar will query `/proc/cpuinfo` for the number of processors).

---

## Comment 28

> Username: raffienficiaud  
> Created_at: 2018-01-28 17:46:47 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361081183  

I have this  
```  
[raffi@sow-fedora27-tests seastar]$ ./build/debug/tests/alloc_test_g  -- --smp 1  
Running 3 test cases...  
WARNING: debug mode. Not for benchmarking or production  
WARN  2018-01-28 18:37:53,038 seastar - Seastar compiled with default allocator, heap profiler not supported  
WARN  2018-01-28 18:37:53,053 [shard 0] seastar - Unable to set SCHED_FIFO scheduling policy for timer thread; latency impact possible. Try adding CAP_SYS_NICE  
=================================================================  
==9015==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60200000b018 at pc 0x000000416c93 bp 0x7f428b1fba70 sp 0x7f428b1fba60  
READ of size 8 at 0x60200000b018 thread T1  
    #0 0x416c92 in submit_to<test_live_objects_counter_with_cross_cpu_free::run_test_case()::<lambda()> > /home/raffi/seastar/core/reactor.hh:1162  
    #1 0x416483 in test_live_objects_counter_with_cross_cpu_free::run_test_case() tests/alloc_test.cc:53  
    #2 0xf13ec3 in operator() tests/test-utils.cc:47  
    #3 0xf36416 in _M_invoke /usr/include/c++/7/bits/std_function.h:302  
    #4 0x7cd8c7 in std::function<seastar::future<> ()>::operator()() const /usr/include/c++/7/bits/std_function.h:706  
    #5 0x10a5a17 in operator() tests/test_runner.cc:76  
    #6 0x10ac4cf in _M_invoke /usr/include/c++/7/bits/std_function.h:302  
    #7 0x7cd8c7 in std::function<seastar::future<> ()>::operator()() const /usr/include/c++/7/bits/std_function.h:706  
    #8 0x10a3818 in operator() tests/test_runner.cc:56  
    #9 0x10b2a49 in run_and_dispose /home/raffi/seastar/core/future-util.hh:464  
    #10 0x4c17bd in seastar::reactor::run_tasks(seastar::reactor::task_queue&) core/reactor.cc:2487  
    #11 0x4c5a5c in seastar::reactor::run_some_tasks(std::chrono::time_point<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > >&) core/reactor.cc:2884  
    #12 0x4cc950 in seastar::reactor::run() core/reactor.cc:3028  
    #13 0xc289be in seastar::app_template::run_deprecated(int, char**, std::function<void ()>&&) core/app-template.cc:180  
    #14 0x10a47df in operator() tests/test_runner.cc:63  
    #15 0x10ad894 in _M_invoke /usr/include/c++/7/bits/std_function.h:316  
    #16 0x7bef2b in std::function<void ()>::operator()() const /usr/include/c++/7/bits/std_function.h:706  
    #17 0xb291b8 in seastar::posix_thread::start_routine(void*) core/posix.cc:52  
    #18 0x7f428fd7a608 in start_thread (/lib64/libpthread.so.0+0x7608)  
    #19 0x7f428faa7e6e in __clone (/lib64/libc.so.6+0x119e6e)  
  
0x60200000b018 is located 0 bytes to the right of 8-byte region [0x60200000b010,0x60200000b018)  
allocated by thread T1 here:  
    #0 0x7f4293d07318 in operator new[](unsigned long) (/lib64/libasan.so.4+0xe0318)  
    #1 0x4e161d in seastar::smp::configure(boost::program_options::variables_map) core/reactor.cc:3994  
    #2 0xc2741d in seastar::app_template::run_deprecated(int, char**, std::function<void ()>&&) core/app-template.cc:162  
    #3 0x10a47df in operator() tests/test_runner.cc:63  
    #4 0x10ad894 in _M_invoke /usr/include/c++/7/bits/std_function.h:316  
    #5 0x7bef2b in std::function<void ()>::operator()() const /usr/include/c++/7/bits/std_function.h:706  
    #6 0xb291b8 in seastar::posix_thread::start_routine(void*) core/posix.cc:52  
    #7 0x7f428fd7a608 in start_thread (/lib64/libpthread.so.0+0x7608)  
  
Thread T1 created by T0 here:  
    #0 0x7f4293c5ea2f in pthread_create (/lib64/libasan.so.4+0x37a2f)  
    #1 0xb29972 in seastar::posix_thread::posix_thread(seastar::posix_thread::attr, std::function<void ()>) core/posix.cc:83  
    #2 0xb29368 in seastar::posix_thread::posix_thread(std::function<void ()>) core/posix.cc:57  
    #3 0x10a890f in make_unique<seastar::posix_thread, seastar::test_runner::start(int, char**)::<lambda()> > /usr/include/c++/7/bits/unique_ptr.h:825  
    #4 0x10a4bda in seastar::test_runner::start(int, char**) tests/test_runner.cc:49  
    #5 0xf152e8 in seastar::init_unit_test_suite() tests/test-utils.cc:77  
    #6 0xeb1666 in invoke_init_func /home/raffi/usr/local/include/boost/test/impl/framework.ipp:181  
    #7 0x10678bf in void boost::_bi::list1<boost::_bi::value<bool (*)()> >::operator()<void (*)(bool (*)()), boost::_bi::list0>(boost::_bi::type<void>, void (*&)(bool (*)()), boost::_bi::list0&, int) /home/raffi/usr/local/include/boost/bind/bind.hpp:259  
    #8 0x1058e41 in boost::_bi::bind_t<void, void (*)(bool (*)()), boost::_bi::list1<boost::_bi::value<bool (*)()> > >::operator()() /home/raffi/usr/local/include/boost/bind/bind.hpp:1294  
    #9 0x103f913 in boost::detail::function::void_function_obj_invoker0<boost::_bi::bind_t<void, void (*)(bool (*)()), boost::_bi::list1<boost::_bi::value<bool (*)()> > >, void>::invoke(boost::detail::function::function_buffer&) /home/raffi/usr/local/include/boost/function/function_template.hpp:159  
    #10 0xf953cb in boost::function0<void>::operator()() const /home/raffi/usr/local/include/boost/function/function_template.hpp:759  
    #11 0xf76685 in boost::detail::forward::operator()() /home/raffi/usr/local/include/boost/test/impl/execution_monitor.ipp:1310  
    #12 0x1045b38 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/raffi/usr/local/include/boost/function/function_template.hpp:138  
    #13 0xfe9eb5 in boost::function0<int>::operator()() const /home/raffi/usr/local/include/boost/function/function_template.hpp:760  
    #14 0xfb0b81 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) /home/raffi/usr/local/include/boost/test/impl/execution_monitor.ipp:285  
    #15 0xec4efb in boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/raffi/usr/local/include/boost/test/impl/execution_monitor.ipp:874  
    #16 0xec53a2 in boost::execution_monitor::execute(boost::function<int ()> const&) /home/raffi/usr/local/include/boost/test/impl/execution_monitor.ipp:1213  
    #17 0xec7c7a in boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/raffi/usr/local/include/boost/test/impl/execution_monitor.ipp:1319  
    #18 0xeb859b in boost::unit_test::framework::init(bool (*)(), int, char**) /home/raffi/usr/local/include/boost/test/impl/framework.ipp:1147  
    #19 0xef2cb6 in boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/raffi/usr/local/include/boost/test/impl/unit_test_main.ipp:189  
    #20 0xf15468 in main tests/test-utils.cc:84  
    #21 0x7f428f9af039 in __libc_start_main (/lib64/libc.so.6+0x21039)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow /home/raffi/seastar/core/reactor.hh:1162 in submit_to<test_live_objects_counter_with_cross_cpu_free::run_test_case()::<lambda()> >  
Shadow bytes around the buggy address:  
  0x0c047fff95b0: fa fa fd fa fa fa 00 00 fa fa fd fd fa fa fd fd  
  0x0c047fff95c0: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff95d0: fa fa 00 00 fa fa 00 00 fa fa fd fd fa fa fd fd  
  0x0c047fff95e0: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff95f0: fa fa 00 00 fa fa 00 00 fa fa 00 fa fa fa 04 fa  
=>0x0c047fff9600: fa fa 00[fa]fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff9610: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff9620: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff9630: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff9640: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
  0x0c047fff9650: fa fa fd fd fa fa fd fd fa fa fd fd fa fa fd fd  
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
==9015==ABORTING  
```

---

## Comment 29

> Username: raffienficiaud  
> Created_at: 2018-01-28 17:53:40 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361081687  

I suggest we check on a test that does not require any specifics. This is why I used the json test.

---

## Comment 30

> Username: avikivity  
> Created_at: 2018-01-29 14:52:09 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361269154  

`json_formatter_test` doesn't trigger the problem for me even with the use-after-freeing version of boost.

---

## Comment 31

> Username: raffienficiaud  
> Created_at: 2018-01-29 14:53:14 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361269501  

But it did for me. How did you "patched" boost?

---

## Comment 32

> Username: avikivity  
> Created_at: 2018-01-29 15:02:06 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361272435  

With the intentional-leak patch in this pull request.

---

## Comment 33

> Username: raffienficiaud  
> Created_at: 2018-01-29 15:15:31 UTC  
> Updated_at: 2018-01-29 15:15:44 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361276795  

Previously you said that you did not provide any additional options to the ``configure`` command, and you tried the version of the branch. How did you manage to patch the system installed boost with the branch?   
  
The intentional leak patch you provide is not going to be included, so I suggest we work together in the following patch (in the mentioned branch) that is cleaning the references to the file streams at shutdown time of boost.test. This is a **clean** solution.  
  
The checks I have done on my end are:  
* compile the json_test with the system boost: problem with asan  
* compile the json_test with a copy of boost.1.64: problems with asan  
* compile the json_test with a copy of boost.1.64 and boost.test compiled from branch ``topic/13371-logstream-use-after-destroy``  
    * using ``--log_sink`` with a file: problem with asan  
    * using ``--logger``: no problem reported  
  
To compile boost  
```  
git clone --recursive https://www.github.com/boostorg/boost   
git checkout boost-1.64.0   
git submodule init  
git submodule update --recursive  
cd libs/test/    
git checkout topic/13371-logstream-use-after-destroy  
cd ../../    
mkdir -p ~/usr/local  
./bootstrap.sh  
./b2 -j4 --prefix=/home/raffi/usr/local/ install  
```  
  
To compile seastar:  
```  
./configure.py --cflags="-I/home/raffi/usr/local/include -L/home/raffi/usr/local/lib -Wl,-rpath=/home/raffi/usr/local/lib" --tests-debuginfo=1     
ninja build/debug/tests/json_formatter_test_g                                                                
```

---

## Comment 34

> Username: avikivity  
> Created_at: 2018-01-29 15:18:52 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361277911  

I patched Fedora's rpm and installed it. I don't trust myself to get things right with the boost build system. See https://copr.fedorainfracloud.org/coprs/avik/boost/build/692456/.  
  
I completely understand why you don't want my patch, and I would have written a better patch if I could. I'm not asking for it to be included, and I fully appreciate the effort you're putting into fixing it.

---

## Comment 35

> Username: avikivity  
> Created_at: 2018-01-29 15:20:28 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361278431  

>compile the json_test with a copy of boost.1.64 and boost.test compiled from branch topic/13371-logstream-use-after-destroy  
>using --log_sink with a file: problem with asan  
>using --logger: no problem reported  
  
So, `--logger` is needed with the patch? I thought it would work without it. I'll test again.

---

## Comment 36

> Username: avikivity  
> Created_at: 2018-01-29 15:24:57 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361279965  

Well, now I feel stupid, I recompiled with Fedora's boost (no patches at all) and now all the tests pass.  
  
Maybe some unrelated change "fixed" the link order.

---

## Comment 37

> Username: raffienficiaud  
> Created_at: 2018-01-29 15:38:52 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361284587  

Indeed ``--logger`` activates the fix in the branch, while ``--log_sink`` does not. Both command should have the problem in 1.64 or master.  
  
I understood from a previous post that you patched the system boost library. I was able to reproduce the problem on my end, and the branch fixed it.   
  
So ... if you have some time, I suggest the following:  
* remove completely boost from the operating system  
* clone boost at 1.64.0 tag, and build it **without the boost.test fix** (see command line of my previous post)  
* configure seastar such that it uses your compiled boost (see command line above, adapt paths)  
* run whatever test, you should see the error with ``--log_sink`` and ``--logger``  
* go back to the boost directory, clone the boost.test branch (see command line of my previous post), and build it  
* configure seastar again, and re-run the same test, , you should see the error with ``--log_sink`` **but not with** ``--logger``  
  
Thanks!

---

## Comment 38

> Username: avikivity  
> Created_at: 2018-01-29 15:47:13 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-361287495  

I'll re-test with the patch and --logger.

---

## Comment 39

> Username: raffienficiaud  
> Created_at: 2018-02-02 09:20:43 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-362531317  

Gentle reminder

---

## Comment 40

> Username: avikivity  
> Created_at: 2018-02-03 19:54:24 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-362849224  

Confirmed:  
 - Fedora's boost 1.64.0 with --log_sink: fails  
 - Fedora's boost 1.64.0 with --logger XML,all,path: fails  
 - Fedora's boost 1.64.0 with your patches and --logger XML,all,path: passes

---

## Comment 41

> Username: raffienficiaud  
> Created_at: 2018-02-04 13:44:00 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-362907830  

Very good, thanks!

---

## Comment 42

> Username: avikivity  
> Created_at: 2018-02-07 14:19:52 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-363782888  

So, what's the plan? Will we have a fix for this soon?

---

## Comment 43

> Username: raffienficiaud  
> Created_at: 2018-02-07 14:20:18 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-363783021  

Yes, this is the plan. For 1.67

---

## Comment 44

> Username: avikivity  
> Created_at: 2018-02-07 14:22:15 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-363783559  

Thanks. When it is committed, please let me know and I'll ask the Fedora boost maintainer to backport the fix.

---

## Comment 45

> Username: raffienficiaud  
> Created_at: 2018-02-20 19:28:38 UTC  
> Url: https://github.com/boostorg/test/pull/124#issuecomment-367091134  

This fix is in master, as indicated [here](https://svn.boost.org/trac10/query?status=assigned&status=new&status=reopened&component=test&col=id&col=summary&col=status&col=type&col=milestone&col=component&report=52&order=priority) . It should be part of **Boost 1.67**.  
  
I am closing this PR as the final solution has little to do with it. Feel free to reopen on trac if something is not working as expected.

---
