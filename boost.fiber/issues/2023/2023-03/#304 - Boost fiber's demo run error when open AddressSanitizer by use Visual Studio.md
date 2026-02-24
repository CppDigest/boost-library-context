# #304 - Boost fiber's demo run error when open AddressSanitizer by use Visual Studio [Open]

> Username: TXTT2016  
> Created at: 2023-03-24 10:03:04 UTC  
> Updated at: 2023-03-31 06:57:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/304  

This is the demo uri: https://github.com/boostorg/fiber/blob/develop/test/test_buffered_channel_post.cpp  
  
The error message:  
  
```code  
==19812==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x06fff9fc at pc 0x0090c2a8 bp 0x06fff9c8 sp 0x06fff9bc  
READ of size 4 at 0x06fff9fc thread T0  
==19812==WARNING: Failed to use and restart external symbolizer!  
    #0 0x90c2a7 in boost::intrusive::list_node_traits<void *>::get_next E:\work\boost_1_72_0\boost\intrusive\detail\list_node.hpp:63  
    boostorg/context#1 0x912d48 in boost::intrusive::circular_list_algorithms<boost::intrusive::list_node_traits<void *> >::unlink E:\work\boost_1_72_0\boost\intrusive\circular_list_algorithms.hpp:143  
    boostorg/context#2 0x8f977d in boost::intrusive::list_impl<boost::intrusive::fhtraits<boost::fibers::detail::wait_functor>,unsigned int,0,void>::pop_front_and_dispose<boost::intrusive::detail::null_disposer> E:\work\boost_1_72_0\boost\intrusive\list.hpp:353  
    boostorg/context#3 0x90fb1f in boost::intrusive::list_impl<boost::intrusive::fhtraits<boost::fibers::detail::wait_functor>,unsigned int,0,void>::pop_front E:\work\boost_1_72_0\boost\intrusive\list.hpp:336  
    boostorg/context#4 0x9abcb8 in boost::fibers::context::terminate E:\work\boost_1_72_0\libs\fiber\src\context.cpp:242  
    boostorg/context#5 0x8ce36d in boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::run_ E:\work\boost_1_72_0\boost\fiber\context.hpp:440  
    boostorg/context#6 0x8f7ab9 in std::invoke<boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*&)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > * &,boost::context::fiber> D:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\type_traits:1540  
    boostorg/context#7 0x8ed559 in std::_Invoker_ret<std::_Unforced,0>::_Call<boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*&)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > * &,boost::context::fiber> D:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\functional:670  
    boostorg/context#8 0x8edad7 in std::_Call_binder<std::_Unforced,0,1,boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*)(boost::context::fiber &&),std::tuple<boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > *,std::_Ph<1> >,std::tuple<boost::context::fiber &&> > D:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\functional:1307  
    boostorg/context#9 0x8eb6e5 in std::_Binder<std::_Unforced,boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > *,std::_Ph<1> const &>::operator()<boost::context::fiber> D:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\functional:1344  
    boostorg/context#10 0x8f8968 in boost::context::detail::invoke<std::_Binder<std::_Unforced,boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > *,std::_Ph<1> const &> &,boost::context::fiber> E:\work\boost_1_72_0\boost\context\detail\invoke.hpp:41  
    boostorg/context#11 0x8d6ebe in boost::context::detail::fiber_capture_record<boost::context::fiber,boost::context::basic_fixedsize_stack<boost::context::stack_traits>,std::_Binder<std::_Unforced,boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > *,std::_Ph<1> const &> >::run E:\work\boost_1_72_0\boost\context\fiber_winfib.hpp:235  
    boostorg/context#12 0x8d7205 in boost::context::detail::fiber_entry_func<boost::context::detail::fiber_capture_record<boost::context::fiber,boost::context::basic_fixedsize_stack<boost::context::stack_traits>,std::_Binder<std::_Unforced,boost::context::fiber (__thiscall boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> >::*)(boost::context::fiber &&),boost::fibers::worker_context<<lambda_394fc3d94239fe53cf412e0cad16890f> > *,std::_Ph<1> const &> > > E:\work\boost_1_72_0\boost\context\fiber_winfib.hpp:62  
    boostorg/context#13 0x75efdab0 in GetNumaHighestNodeNumber+0xa0 (C:\Windows\System32\KERNELBASE.dll+0x1012dab0)  
    boostorg/context#14 0x75efda65 in GetNumaHighestNodeNumber+0x55 (C:\Windows\System32\KERNELBASE.dll+0x1012da65)  
    boostorg/context#15 0x77ab2796 in RtlUserFiberStart+0x16 (C:\Windows\SYSTEM32\ntdll.dll+0x4b2f2796)  
  
Address 0x06fff9fc is a wild pointer.  
SUMMARY: AddressSanitizer: stack-buffer-overflow E:\work\boost_1_72_0\boost\intrusive\detail\list_node.hpp:63 in boost::intrusive::list_node_traits<void *>::get_next  
Shadow bytes around the buggy address:  
  0x30dffee0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x30dffef0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x30dfff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x30dfff10: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x30dfff20: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x30dfff30: 00 00 00 00 00 00 00 00 00 00 f1 f1 04 f2 04[f2]  
  0x30dfff40: f8 f3 f1 f1 04 f2 f8 f3 f3 f3 f3 00 00 00 00 00  
  0x30dfff50: 00 00 00 00 00 00 00 00 f1 f1 04 f2 04 f2 f1 f1  
  0x30dfff60: f8 f2 f8 f2 f8 f3 f3 f3 f3 00 00 00 00 00 00 00  
  0x30dfff70: 00 00 00 00 00 00 f1 f1 04 f3 f3 f3 f3 00 00 00  
  0x30dfff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 f1 f1  
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
  Shadow gap:              cc  
```

---

## Comment 1

> Username: TXTT2016  
> Created at: 2023-03-31 06:57:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/304#issuecomment-1491406400  

anyone help?
