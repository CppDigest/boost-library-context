# #33 - Failed to reflect containers with std::unique_ptr [Closed]

> Username: filcuc  
> Created at: 2019-01-11 15:13:02 UTC  
> Updated at: 2019-01-23 20:32:09 UTC  
> Closed at: 2019-01-23 20:26:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/33  

Issue #27 and #30 solved the problem of reflecting std::unique_ptr  
But it seems that this is not applied in case of containers.  
The following snipper  
```  
#include <iostream>  
#include <vector>  
#include <boost/pfr.hpp>  
  
using namespace std;  
  
struct TestStruct {  
  std::vector<std::unique_ptr<int>> prova;  
};  
  
int main()  
{  
  TestStruct temp;  
  boost::pfr::for_each_field(temp, [](const auto& value){  
  });  
}  
```  
does not compile with the following error  
```  
distcc /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux-gnueabi/arm-poky-linux-gnueabi-g++ --sysroot=/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi    -march=armv7-a -marm -mfpu=neon  -mfloat-abi=hard -mcpu=cortex-a9 -g -MD -MT CMakeFiles/untitled.dir/main.cpp.o -MF CMakeFiles/untitled.dir/main.cpp.o.d -o CMakeFiles/untitled.dir/main.cpp.o -c /tmp/untitled/main.cpp  
distcc[16414] Warning: INCLUDE_SERVER_PORT not set - did you forget to run under 'distcc-pump'?  
distcc[16414] (dcc_build_somewhere) Warning: failed to get includes from include server, preprocessing locally  
distcc[16414] ERROR: compile /tmp/untitled/main.cpp on ap-ThinkPad-T520.local,lzo,cpp failed  
distcc[16414] (dcc_build_somewhere) Warning: remote compilation of '/tmp/untitled/main.cpp' failed, retrying locally  
distcc[16414] Warning: failed to distribute /tmp/untitled/main.cpp to ap-ThinkPad-T520.local,lzo,cpp, running locally instead  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/vector:62:0,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_construct.h: In instantiation of ‘void std::_Construct(_T1*, _Args&& ...) [with _T1 = std::unique_ptr<int>; _Args = {const std::unique_ptr<int, std::default_delete<int> >&}]’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_uninitialized.h:75:18:   required from ‘static _ForwardIterator std::__uninitialized_copy<_TrivialValueTypes>::__uninit_copy(_InputIterator, _InputIterator, _ForwardIterator) [with _InputIterator = __gnu_cxx::__normal_iterator<const std::unique_ptr<int>*, std::vector<std::unique_ptr<int> > >; _ForwardIterator = std::unique_ptr<int>*; bool _TrivialValueTypes = false]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_uninitialized.h:126:15:   required from ‘_ForwardIterator std::uninitialized_copy(_InputIterator, _InputIterator, _ForwardIterator) [with _InputIterator = __gnu_cxx::__normal_iterator<const std::unique_ptr<int>*, std::vector<std::unique_ptr<int> > >; _ForwardIterator = std::unique_ptr<int>*]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_uninitialized.h:281:37:   required from ‘_ForwardIterator std::__uninitialized_copy_a(_InputIterator, _InputIterator, _ForwardIterator, std::allocator<_Tp>&) [with _InputIterator = __gnu_cxx::__normal_iterator<const std::unique_ptr<int>*, std::vector<std::unique_ptr<int> > >; _ForwardIterator = std::unique_ptr<int>*; _Tp = std::unique_ptr<int>]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_vector.h:324:31:   required from ‘std::vector<_Tp, _Alloc>::vector(const std::vector<_Tp, _Alloc>&) [with _Tp = std::unique_ptr<int>; _Alloc = std::allocator<std::unique_ptr<int> >]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:70:117:   required from ‘auto boost::pfr::detail::loophole(boost::pfr::detail::tag<TestStruct, 0u>)’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:118:58:   required from ‘struct boost::pfr::detail::loophole_type_list_lref<TestStruct, std::integer_sequence<unsigned int, 0u> >’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:152:57:   required from ‘auto boost::pfr::detail::tie_as_tuple_loophole_impl(T&) [with T = TestStruct]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:255:55:   required from ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<I ...>) [with T = TestStruct; F = boost::pfr::for_each_field(T&&, F&&) [with T = TestStruct&; F = main()::<lambda(const auto:7&)>]::<lambda(auto:1&&)>; unsigned int ...I = {0u}; std::index_sequence<I ...> = std::integer_sequence<unsigned int, 0u>]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:140:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = TestStruct&; F = main()::<lambda(const auto:7&)>]’  
/tmp/untitled/main.cpp:15:4:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/stl_construct.h:75:7: error: use of deleted function ‘std::unique_ptr<_Tp, _Dp>::unique_ptr(const std::unique_ptr<_Tp, _Dp>&) [with _Tp = int; _Dp = std::default_delete<int>]’  
     { ::new(static_cast<void*>(__p)) _T1(std::forward<_Args>(__args)...); }  
       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/locale_conv.h:41:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/locale:43,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/iomanip:43,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/io.hpp:14,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/io.hpp:17,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/ops.hpp:15,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:14,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:3:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/unique_ptr.h:359:7: note: declared here  
       unique_ptr(const unique_ptr&) = delete;  
       ^~~~~~~~~~  
distcc[16414] ERROR: compile /tmp/untitled/main.cpp on localhost failed  
ninja: build stopped: subcommand failed.  
16:10:31: The process "/home/cucchetf/tools/cmake/bin/cmake" exited with code 1.  
Error while building/deploying project untitled (kit: Private SDK)  
The kit Private SDK has configuration issues which might be the root cause for this problem.  
When executing step "CMake Build"  
```

---

## Comment 1

> Username: filcuc  
> Created at: 2019-01-11 15:13:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/33#issuecomment-453548722  

GCC 6.3.0 arm gnueabihf

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-01-23 20:32:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/33#issuecomment-456955077  

Many thanks for the report!  
  
This issue is actually an issue in the C++ Standard. In the [[container.requirements]](http://eel.is/c++draft/container.requirements) section of the Standard all the `Requires:` should be replaced with `Constraints:`. This is a known problem for the WG21 committee. Hopefully it will be resolved soon.  
  
Anyway, I've added a workaround in https://github.com/apolukhin/magic_get/commit/6b03224636629a4b8f399fcc3cb3b5b10efe06fd. It should do the job.
