# #265 - Problem building with latest MSVC [Closed]

> Username: mclow  
> Created at: 2020-11-03 16:38:43 UTC  
> Updated at: 2020-11-15 18:15:12 UTC  
> Closed at: 2020-11-14 06:23:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/265  

Tom Kent wrote on the mailing list (27-Oct):  
  
I tried building on windows/visual studio from the nightly snapshot. There  
are errors for msvc-14.0, 14.1, and 14.2. See example below.  
  
Tom  
  
compile-c-c++  
D:\RB\bin.v2\boost\bin.v2\libs\fiber\build\msvc-14.1\release\address-model-64\threading-multi\algo\algorithm.obj  
algorithm.cpp  
.\boost/fiber/algo/algorithm.hpp(33): warning C4251:  
'boost::fibers::algo::algorithm::use_count_': struct 'std::atomic<unsigned  
__int64>' needs to have dll-interface to be used by clients of class  
'boost::fibers::algo::algorithm'  
C:\Program Files (x86)\Microsoft Visual  
Studio\2017\BuildTools\VC\Tools\MSVC\14.16.27023\include\xxatomic(162):  
note: see declaration of 'std::atomic<unsigned __int64>'  
.\boost/fiber/waker.hpp(58): warning C4251:  
'boost::fibers::waker_with_hook::waker_queue_hook_': class  
'boost::intrusive::slist_member_hook<>' needs to have dll-interface to be  
used by clients of class 'boost::fibers::waker_with_hook'  
.\boost/fiber/waker.hpp(18): note: see declaration of  
'boost::intrusive::slist_member_hook<>'  
.\boost/fiber/waker.hpp(72): warning C4251:  
'boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>::data_':  
struct  
'boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>::data_t'  
needs to have dll-interface to be used by clients of class  
'boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>'  
       with  
       [  
           Parent=boost::fibers::waker_with_hook,  
           MemberHook=boost::fibers::detail::waker_queue_hook  
       ]  
.\boost/intrusive/slist.hpp(236): note: see declaration of  
'boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>::data_t'  
       with  
       [  
           Parent=boost::fibers::waker_with_hook,  
           MemberHook=boost::fibers::detail::waker_queue_hook  
       ]  
.\boost/intrusive/slist.hpp(2113): error C2039: 'swap_trailing_nodes': is  
not a member of 'boost::intrusive::circular_slist_algorithms<NodeTraits>'  
       with  
       [  
  
NodeTraits=boost::intrusive::slist_node_traits<boost::intrusive::hook_defaults::void_pointer>  
       ]  
.\boost/intrusive/circular_slist_algorithms.hpp(396): note: see declaration  
of 'boost::intrusive::circular_slist_algorithms<NodeTraits>'  
       with  
       [  
  
NodeTraits=boost::intrusive::slist_node_traits<boost::intrusive::hook_defaults::void_pointer>  
       ]  
.\boost/intrusive/slist.hpp(2113): note: while compiling class template  
member function 'void  
boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>::priv_swap_lists(boost::intrusive::slist_node<VoidPointer>  
*,boost::intrusive::slist_node<VoidPointer>  
*,boost::move_detail::bool_<true>)'  
       with  
       [  
           Parent=boost::fibers::waker_with_hook,  
           MemberHook=boost::fibers::detail::waker_queue_hook,  
           VoidPointer=boost::intrusive::hook_defaults::void_pointer  
       ]  
.\boost/intrusive/slist.hpp(2182): note: see reference to class template  
instantiation  
'boost::intrusive::slist_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x10)>,boost::intrusive::slist_defaults::size_type,4,boost::intrusive::slist_defaults::header_holder_type>'  
being compiled  
       with  
       [  
           Parent=boost::fibers::waker_with_hook,  
           MemberHook=boost::fibers::detail::waker_queue_hook  
       ]  
.\boost/fiber/waker.hpp(72): note: see reference to class template  
instantiation  
'boost::intrusive::slist<boost::fibers::waker_with_hook,boost::intrusive::member_hook<boost::fibers::waker_with_hook,boost::fibers::detail::waker_queue_hook,pointer-to-member(0x10)>,boost::intrusive::constant_time_size<false>,boost::intrusive::cache_last<true>>'  
being compiled  
.\boost/intrusive/slist.hpp(2113): error C3861: 'swap_trailing_nodes':  
identifier not found  
  
   call  
"D:\RB\bin.v2\boost\bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  
nul  
cl /Zm800 -nologo  
@"D:\RB\bin.v2\boost\bin.v2\libs\fiber\build\msvc-14.1\release\address-model-64\threading-multi\algo\algorithm.obj.rsp"  
  
  
...failed compile-c-c++  
D:\RB\bin.v2\boost\bin.v2\libs\fiber\build\msvc-14.1\release\address-model-64\threading-multi\algo\algorithm.obj...

---

## Comment 1

> Username: olk  
> Created at: 2020-11-03 16:55:45 UTC  
> Updated at: 2020-11-03 16:59:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-721252179  

\boost/intrusive/slist.hpp(2113): error C3861: 'swap_trailing_nodes':  
identifier not found  
  
seams to me a problem of boost.intrusive - builds successfully with gcc-10 and clang-10

---

## Comment 2

> Username: mclow  
> Created at: 2020-11-06 22:04:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723320502  

Is this solved? or just not a Boost.Fiber problem?  If the latter, how about we transfer the issue to Boost.Intrusive?

---

## Comment 3

> Username: olk  
> Created at: 2020-11-07 10:28:47 UTC  
> Updated at: 2020-11-07 10:33:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723428330  

As I wrote at the dev mailing list I believe it is an issue of boost.intrusive (or MSVC):  
  
```  
.\boost/intrusive/slist.hpp(2113): error C3861: 'swap_trailing_nodes':  
identifier not found  
```  
  
swap_trailing_nodes is defined in linear_slist_algorithms<>.  
It seams that in slist.hpp static const bool linear = 0 != (BoolFlags & slist_bool_flags::linear_pos) evaluates to false using MSVC (it evaluates to true for gcc-10/clang-10).  
If static bool linear is evaluated to false typedef node_algorithms is of type circular_slist_algorithms<> (that doesn't provide swap_trailing_nodes) instead of linear_slist_algorithms<>.

---

## Comment 4

> Username: olk  
> Created at: 2020-11-07 10:29:26 UTC  
> Updated at: 2020-11-07 10:29:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723428395  

I don't know how to transfer issues between projects - sorry

---

## Comment 5

> Username: igaztanaga  
> Created at: 2020-11-07 15:34:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723460019  

Thank for the ping. Looks like a MSVC instantiation bug, I'll have some time to work on this in a few hours to find a workaround.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2020-11-07 21:57:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723500260  

I am missing something because I can't reproduce the bug with msvc-14.1 and 14.2. Command prompt/cl.exe versions are:  
  
> msvc-14.1  
> Visual Studio 2017 Developer Command Prompt v15.9.28  
>  cl.exe -> versión 19.16.27043  
  
> msvc-14.2  
> Visual Studio 2019 Developer Command Prompt v16.7.7  
> cl.exe -> versión 19.27.29112  
  
  
> b2 toolset=msvc-14.2 variant=release address-model=64 threading=multi --with-fiber stage  
  
   
> Performing configuration checks  
>   
>     - default address-model    : 32-bit  
>     - default architecture     : x86  
>     - C++11 mutex              : yes  
>     - has BCrypt API           : yes  
>     - has stat::st_mtim        : no  
>     - has stat::st_mtimensec   : no  
>     - has stat::st_mtimespec   : no  
>     - has stat::st_birthtim    : no  
>     - has stat::st_birthtimensec : no  
>     - has stat::st_birthtimespec : no  
>     - has statx                : no  
>     - has statx syscall        : no  
>     - cxx11_auto_declarations  : yes  
>     - cxx11_constexpr          : yes  
>     - cxx11_defaulted_functions : yes  
>     - cxx11_final              : yes  
>     - cxx11_hdr_mutex          : yes  
>     - cxx11_hdr_tuple          : yes  
>     - cxx11_lambdas            : yes  
>     - cxx11_noexcept           : yes  
>     - cxx11_nullptr            : yes  
>     - cxx11_rvalue_references  : yes  
>     - cxx11_template_aliases   : yes  
>     - cxx11_thread_local       : yes  
>     - cxx11_variadic_templates : yes  
>   
> Component configuration:  
>   
>     - atomic                   : not building  
>     - chrono                   : not building  
>     - container                : not building  
>     - context                  : not building  
>     - contract                 : not building  
>     - coroutine                : not building  
>     - date_time                : not building  
>     - exception                : not building  
>     - fiber                    : building  
>     - filesystem               : not building  
>     - graph                    : not building  
>     - graph_parallel           : not building  
>     - headers                  : not building  
>     - iostreams                : not building  
>     - json                     : not building  
>     - locale                   : not building  
>     - log                      : not building  
>     - math                     : not building  
>     - mpi                      : not building  
>     - nowide                   : not building  
>     - program_options          : not building  
>     - python                   : not building  
>     - random                   : not building  
>     - regex                    : not building  
>     - serialization            : not building  
>     - stacktrace               : not building  
>     - system                   : not building  
>     - test                     : not building  
>     - thread                   : not building  
>     - timer                    : not building  
>     - type_erasure             : not building  
>     - wave                     : not building  
>   
> ...patience...  
> ...found 2174 targets...  
> ...updating 93 targets...  
> msvc.compile.asm bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\asm\make_x86_64_ms_pe_masm.obj  
>  Assembling: libs\context\src\asm\make_x86_64_ms_pe_masm.asm  
> boost-install.generate-cmake-config-version- bin.v2\tools\boost_install\BoostConfigVersion.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\Boost-1.75.0\BoostConfigVersion.cmake  
> bin.v2\tools\boost_install\BoostConfigVersion.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config- bin.v2\libs\context\build\stage\boost_context-config.cmake  
> boost-install.generate-cmake-config-version- bin.v2\libs\context\build\stage\boost_context-config-version.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_context-1.75.0\boost_context-config.cmake  
> bin.v2\libs\context\build\stage\boost_context-config.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config- bin.v2\libs\headers\build\stage\boost_headers-config.cmake  
> boost-install.generate-cmake-config- bin.v2\libs\filesystem\build\stage\boost_filesystem-config.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_context-1.75.0\boost_context-config-version.cmake  
> bin.v2\libs\context\build\stage\boost_context-config-version.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config-version- bin.v2\libs\filesystem\build\stage\boost_filesystem-config-version.cmake  
> boost-install.generate-cmake-config- bin.v2\libs\fiber\build\stage\boost_fiber-config.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_headers-1.75.0\boost_headers-config.cmake  
> bin.v2\libs\headers\build\stage\boost_headers-config.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config-version- bin.v2\libs\fiber\build\stage\boost_fiber-config-version.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_filesystem-1.75.0\boost_filesystem-config.cmake  
> bin.v2\libs\filesystem\build\stage\boost_filesystem-config.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config- bin.v2\libs\fiber\build\stage\boost_fiber_numa-config.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_filesystem-1.75.0\boost_filesystem-config-version.cmake  
> bin.v2\libs\filesystem\build\stage\boost_filesystem-config-version.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config-version- bin.v2\libs\fiber\build\stage\boost_fiber_numa-config-version.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_fiber-1.75.0\boost_fiber-config-version.cmake  
> bin.v2\libs\fiber\build\stage\boost_fiber-config-version.cmake  
>         1 archivo(s) copiado(s).  
> boost-install.generate-cmake-config-version- bin.v2\libs\headers\build\stage\boost_headers-config-version.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_fiber_numa-1.75.0\boost_fiber_numa-config.cmake  
> bin.v2\libs\fiber\build\stage\boost_fiber_numa-config.cmake  
>         1 archivo(s) copiado(s).  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_fiber-1.75.0\boost_fiber-config.cmake  
> bin.v2\libs\fiber\build\stage\boost_fiber-config.cmake  
>         1 archivo(s) copiado(s).  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_fiber_numa-1.75.0\boost_fiber_numa-config-version.cmake  
> bin.v2\libs\fiber\build\stage\boost_fiber_numa-config-version.cmake  
>         1 archivo(s) copiado(s).  
> compile-c-c++ bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\dummy.obj  
> dummy.cpp  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_headers-1.75.0\boost_headers-config-version.cmake  
> bin.v2\libs\headers\build\stage\boost_headers-config-version.cmake  
>         1 archivo(s) copiado(s).  
> msvc.compile.asm bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\asm\jump_x86_64_ms_pe_masm.obj  
>  Assembling: libs\context\src\asm\jump_x86_64_ms_pe_masm.asm  
> msvc.compile.asm bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\asm\ontop_x86_64_ms_pe_masm.obj  
>  Assembling: libs\context\src\asm\ontop_x86_64_ms_pe_masm.asm  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\utf8_codecvt_facet.obj  
> utf8_codecvt_facet.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\codecvt_error_category.obj  
> codecvt_error_category.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\path_traits.obj  
> path_traits.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\future.obj  
> future.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\exception.obj  
> exception.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\portability.obj  
> portability.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\unique_path.obj  
> unique_path.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\windows_file_codecvt.obj  
> windows_file_codecvt.cpp  
> compile-c-c++ bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\windows\stack_traits.obj  
> stack_traits.cpp  
> msvc.archive bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_context-vc142-mt-x64-1_75.lib  
> boost-install.generate-cmake-variant- bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_context-variant-vc142-mt-x64-1_75-static.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\libboost_context-vc142-mt-x64-1_75.lib  
> bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_context-vc142-mt-x64-1_75.lib  
>         1 archivo(s) copiado(s).  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_context-1.75.0\libboost_context-variant-vc142-mt-x64-1_75-static.cmake  
> bin.v2\libs\context\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_context-variant-vc142-mt-x64-1_75-static.cmake  
>         1 archivo(s) copiado(s).  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\path.obj  
> path.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\directory.obj  
> directory.cpp  
> compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\operations.obj  
> operations.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\condition_variable.obj  
> condition_variable.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\barrier.obj  
> barrier.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\algo\algorithm.obj  
> algorithm.cpp  
> msvc.archive bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_filesystem-vc142-mt-x64-1_75.lib  
> boost-install.generate-cmake-variant- bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_filesystem-variant-vc142-mt-x64-1_75-static.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\libboost_filesystem-vc142-mt-x64-1_75.lib  
> bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_filesystem-vc142-mt-x64-1_75.lib  
>         1 archivo(s) copiado(s).  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\properties.obj  
> properties.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\timed_mutex.obj  
> timed_mutex.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\fiber.obj  
> fiber.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\waker.obj  
> waker.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\mutex.obj  
> mutex.cpp  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_filesystem-1.75.0\libboost_filesystem-variant-vc142-mt-x64-1_75-static.cmake  
> bin.v2\libs\filesystem\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_filesystem-variant-vc142-mt-x64-1_75-static.cmake  
>         1 archivo(s) copiado(s).  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\recursive_mutex.obj  
> recursive_mutex.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\recursive_timed_mutex.obj  
> recursive_timed_mutex.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\algo\round_robin.obj  
> round_robin.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\scheduler.obj  
> scheduler.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\context.obj  
> context.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\algo\shared_work.obj  
> shared_work.cpp  
> compile-c-c++ bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\algo\work_stealing.obj  
> work_stealing.cpp  
> msvc.archive bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_fiber-vc142-mt-x64-1_75.lib  
> boost-install.generate-cmake-variant- bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_fiber-variant-vc142-mt-x64-1_75-static.cmake  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\libboost_fiber-vc142-mt-x64-1_75.lib  
> bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_fiber-vc142-mt-x64-1_75.lib  
>         1 archivo(s) copiado(s).  
> common.copy C:\Data\Libs\boost_1_75_0_b1_rc1\stage\lib\cmake\boost_fiber-1.75.0\libboost_fiber-variant-vc142-mt-x64-1_75-static.cmake  
> bin.v2\libs\fiber\build\msvc-14.2\release\address-model-64\link-static\threading-multi\libboost_fiber-variant-vc142-mt-x64-1_75-static.cmake  
>         1 archivo(s) copiado(s).  
> ...updated 93 targets...  
>

---

## Comment 7

> Username: pdimov  
> Created at: 2020-11-07 23:14:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723507023  

Confirmed; `b2 --with-fiber address-model=64 variant=release` works for me with both msvc-14.1 and msvc-14.2. With msvc-14.0, I have an error, but it's not the same one.  
```  
compile-c-c++ bin.v2\libs\fiber\build\msvc-14.0\release\address-model-64\link-static\threading-multi\waker.obj  
waker.cpp  
.\boost/fiber/detail/spinlock_ttas_adaptive.hpp(48): error C2039: 'min': is not a member of 'std'  
```

---

## Comment 8

> Username: pdimov  
> Created at: 2020-11-07 23:20:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723507537  

`b2 --with-fiber address-model=64 variant=release link=shared` reproduces the errors.

---

## Comment 9

> Username: pdimov  
> Created at: 2020-11-07 23:23:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-723507813  

https://github.com/boostorg/fiber/issues/264

---

## Comment 10

> Username: igaztanaga  
> Created at: 2020-11-10 22:58:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725017800  

I started fixing the compilation errors as fiber in MSVC requires to instantiate things that in theory are not supported to be instantiated for circular lists, but after "swap_trailing_nodes" error any other unsupported instantiation (including static asserts) are triggered. boost::intrusive::slist supports some member only if the right options are used (this is documented and checked with some static asserts).  
  
So it seems that boost::fiber::wait_queue (new in Boost 1.75), derives publicly from a boost::intrusive list. This provokes C4275 as intrusive classes are templates and not supported by be dllexported. Reviewing similar issues in the net, it seems that exported classes that inherit from a template instantiation that is not explicitly exported  then the compiler implicitly applies dllexport to this template instantiation, which provokes the compilation errors (I think). Since wait_queue is marked as BOOST_FIBERS_DECL, MSVC instantiates all functions of the class template boost::intrusive::slist, including the unsupported ones for circular lists. Changing the slist from being a base class to a member seems to avoid the instantiation problem:  
  
```  
class BOOST_FIBERS_DECL wait_queue  
{  
    detail::waker_slist_t slist_{};  
public:  
    void suspend_and_wait( detail::spinlock_lock &, context *);  
    bool suspend_and_wait_until( detail::spinlock_lock &,  
                                 context *,  
                                 std::chrono::steady_clock::time_point const&);  
    void notify_one();  
    void notify_all();  
};  
```  
instead of   
  
```  
class BOOST_FIBERS_DECL wait_queue : public detail::waker_slist_t  
{  
    using base = detail::waker_slist_t;  
public:  
    using base::base;  
  
    void suspend_and_wait( detail::spinlock_lock &, context *);  
    bool suspend_and_wait_until( detail::spinlock_lock &,  
                                 context *,  
                                 std::chrono::steady_clock::time_point const&);  
    void notify_one();  
    void notify_all();  
};  
```  
  
(the implementation or waker.cpp must be changed accordingly, replacing `base::xxx()` calls with `slist_.xxx()`).  
  
So it seems that this is a Fiber issue when exporting the wait_queue class, but with an easy solution.

---

## Comment 11

> Username: olk  
> Created at: 2020-11-11 05:44:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725213497  

Compiling the code with gcc or clang on Linux I don't get the "swap_trailing_nodes" error which lets me conclude that linear list has been chosen in slist. But MSVC on Windows uses the circular list which raises the "swap_trailing_nodes" error.  
I don't understand the reason of this difference.

---

## Comment 12

> Username: igaztanaga  
> Created at: 2020-11-11 06:41:51 UTC  
> Updated at: 2020-11-11 07:07:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725234745  

The difference is that according to MSVC documentation any template class used as a base class for an exported object is also exported, which  means it is fully instantiated by the compiler. Boost.Intrusive classes don't support instantiation of all members because some member functions have sense only with some options. This is similar to a std::vector, you can't instantiate all members that require copy constructible types for std::vector<MovableOnly>, but if you put such type as the base class of an dll-exported class in MSVC you'll get a compilation error in those functions. That's why putting the type as a member solves the problem: the compiler does not fully instantiate the type. More information:  
  
[General Rules and Limitations](https://docs.microsoft.com/en-us/cpp/c-language/rules-and-limitations-for-dllimport-dllexport?view=msvc-160)  
  
> "Because this is common pattern with templates, the compiler changed the semantics of dllexport when it is applied to a class that has one or more base-classes and when one or more of the base classes is a specialization of a class template. In this case, the compiler implicitly applies dllexport to the specializations of class templates."  
  
DLL-export is out of the standard so rules between GCC/Clang and MSVC might differ.

---

## Comment 13

> Username: olk  
> Created at: 2020-11-11 07:24:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725255203  

I've pushed a fixed but I don't use Windows. Could someone check if the issue is fixed, please?

---

## Comment 14

> Username: pdimov  
> Created at: 2020-11-11 14:33:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725456516  

> DLL-export is out of the standard so rules between GCC/Clang and MSVC might differ.  
  
clang-cl (toolset=clang-win) gives the same error.

---

## Comment 15

> Username: pdimov  
> Created at: 2020-11-11 14:36:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725458187  

Latest develop fails with a bunch of  
```  
libs\fiber\src\scheduler.cpp(40): error C2039: 'empty': is not a member of 'boost::fibers::wait_queue'  
C:\boost-git\develop\boost/fiber/waker.hpp(71): note: see declaration of 'boost::fibers::wait_queue'  
libs\fiber\src\scheduler.cpp(231): error C2039: 'empty': is not a member of 'boost::fibers::wait_queue'  
C:\boost-git\develop\boost/fiber/waker.hpp(71): note: see declaration of 'boost::fibers::wait_queue'  
```

---

## Comment 16

> Username: olk  
> Created at: 2020-11-11 15:25:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725486232  

next try

---

## Comment 17

> Username: pdimov  
> Created at: 2020-11-11 15:45:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725497376  

Compiles now, with a number of 4251 ("needs a DLL interface") warnings.

---

## Comment 18

> Username: pdimov  
> Created at: 2020-11-11 15:47:28 UTC  
> Updated at: 2020-11-11 15:47:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725498733  

The tests pass, but the Jamfile hardcodes `<link>static`. I'm running them now with link=shared and this removed.

---

## Comment 19

> Username: pdimov  
> Created at: 2020-11-11 15:50:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725500175  

link=shared tests fail with  
```  
msvc.link ..\..\bin.v2\libs\fiber\test\test_condition_mt_dispatch_native.test\msvc-14.2\release\context-impl-winfib\threadapi-win32\threading-multi\test_condition_mt_dispatch_native.exe  
   Creating library ..\..\bin.v2\libs\fiber\test\test_condition_mt_dispatch_native.test\msvc-14.2\release\context-impl-winfib\threadapi-win32\threading-multi\test_condition_mt_dispatch_native.lib and object ..\..\bin.v2\libs\fiber\test\test_condition_mt_dispatch_native.test\msvc-14.2\release\context-impl-winfib\threadapi-win32\threading-multi\test_condition_mt_dispatch_native.exp  
MSVCRT.lib(exe_main.obj) : error LNK2019: unresolved external symbol main referenced in function "int __cdecl __scrt_common_main_seh(void)" (?__scrt_common_main_seh@@YAHXZ)  
..\..\bin.v2\libs\fiber\test\test_condition_mt_dispatch_native.test\msvc-14.2\release\context-impl-winfib\threadapi-win32\threading-multi\test_condition_mt_dispatch_native.exe : fatal error LNK1120: 1 unresolved externals  
```  
which seems unrelated.

---

## Comment 20

> Username: olk  
> Created at: 2020-11-11 20:04:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725633285  

Maybe msvcrt.lib is missing...

---

## Comment 21

> Username: mclow  
> Created at: 2020-11-11 21:33:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725673601  

@olk please merge this to master. thanks.

---

## Comment 22

> Username: olk  
> Created at: 2020-11-11 21:49:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725679909  

done

---

## Comment 23

> Username: pdimov  
> Created at: 2020-11-11 22:21:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725693802  

No, a `main` function is missing. My guess is that when using the DLL version of Boost.Test you need to supply a `main` function somehow, as it can't come from a DLL (the static version of Boost.Test probably supplies `main`.)

---

## Comment 24

> Username: pdimov  
> Created at: 2020-11-11 22:24:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-725694939  

https://www.boost.org/doc/libs/1_74_0/libs/test/doc/html/boost_test/utf_reference/link_references/link_boost_test_main_macro.html

---

## Comment 25

> Username: igaztanaga  
> Created at: 2020-11-13 22:00:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-727057311  

Can we transfer this back to fiber?

---

## Comment 26

> Username: pdimov  
> Created at: 2020-11-13 22:09:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-727060641  

Done.

---

## Comment 27

> Username: pdimov  
> Created at: 2020-11-14 14:30:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-727215260  

You're not going to fix the tests to work with link=shared?

---

## Comment 28

> Username: olk  
> Created at: 2020-11-15 17:16:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-727604278  

I've no idea because I don't develop/use MS Windows - but you are welcome to provide a fix.

---

## Comment 29

> Username: pdimov  
> Created at: 2020-11-15 18:15:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/265#issuecomment-727612614  

It doesn't work under Linux either.
