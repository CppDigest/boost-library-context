# #34 - Invalid struct size with tuple with one type not trivial [Closed]

> Username: filcuc  
> Created at: 2019-01-15 17:03:08 UTC  
> Updated at: 2019-01-23 20:35:55 UTC  
> Closed at: 2019-01-23 20:33:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/34  

Hi,  
this works  
```  
#include <iostream>  
#include <boost/pfr.hpp>  
  
using namespace std;  
  
struct Message {  
  std::tuple<int, std::string> temp;  
};  
  
  
int main() {  
  Message msg;  
  boost::pfr::for_each_field(msg, [](const auto & field) {});  
  return 0;  
}  
```  
  
and this one too  
```  
#include <iostream>  
#include <boost/pfr.hpp>  
  
using namespace std;  
  
struct Message {  
  std::tuple<std::string, int> temp;  
};  
  
  
int main() {  
  Message msg;  
  boost::pfr::for_each_field(msg, [](const auto & field) {});  
  return 0;  
}  
```  
  
and also this  
```  
#include <iostream>  
#include <boost/pfr.hpp>  
  
using namespace std;  
  
struct Message {  
  std::tuple<int> temp;  
};  
  
  
int main() {  
  Message msg;  
  boost::pfr::for_each_field(msg, [](const auto & field) {});  
  return 0;  
}  
```  
  
but this one NO!!  
```  
#include <iostream>  
#include <boost/pfr.hpp>  
  
using namespace std;  
  
struct Message {  
  std::tuple<std::string> temp;  
};  
  
  
int main() {  
  Message msg;  
  boost::pfr::for_each_field(msg, [](const auto & field) {});  
  return 0;  
}  
  
```  
  
This is the output  
```  
18:02:47: Running steps for project untitled...  
18:02:47: Starting: "/home/cucchetf/tools/cmake/bin/cmake" --build . --target all  
[1/2 0.5/sec] Building CXX object CMakeFiles/untitled.dir/main.cpp.o  
FAILED: CMakeFiles/untitled.dir/main.cpp.o   
distcc /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux-gnueabi/arm-poky-linux-gnueabi-g++ --sysroot=/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi    -march=armv7-a -marm -mfpu=neon  -mfloat-abi=hard -mcpu=cortex-a9 -g -MD -MT CMakeFiles/untitled.dir/main.cpp.o -MF CMakeFiles/untitled.dir/main.cpp.o.d -o CMakeFiles/untitled.dir/main.cpp.o -c /tmp/untitled/main.cpp  
distcc[16882] Warning: INCLUDE_SERVER_PORT not set - did you forget to run under 'distcc-pump'?  
distcc[16882] (dcc_build_somewhere) Warning: failed to get includes from include server, preprocessing locally  
distcc[16882] ERROR: compile /tmp/untitled/main.cpp on 127.0.0.1,lzo,cpp failed  
distcc[16882] (dcc_build_somewhere) Warning: remote compilation of '/tmp/untitled/main.cpp' failed, retrying locally  
distcc[16882] Warning: failed to distribute /tmp/untitled/main.cpp to 127.0.0.1,lzo,cpp, running locally instead  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:29:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:24,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/offset_based_getter.hpp: In instantiation of ‘class boost::pfr::detail::offset_based_getter<Message, boost::pfr::detail::sequence_tuple::tuple<std::allocator<char> > >’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:154:61:   required from ‘auto boost::pfr::detail::tie_as_tuple_loophole_impl(T&) [with T = Message]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:255:55:   required from ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<I ...>) [with T = Message; F = boost::pfr::for_each_field(T&&, F&&) [with T = Message&; F = main()::<lambda(const auto:7&)>]::<lambda(auto:1&&)>; unsigned int ...I = {0u}; std::index_sequence<I ...> = std::integer_sequence<unsigned int, 0u>]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:140:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = Message&; F = main()::<lambda(const auto:7&)>]’  
/tmp/untitled/main.cpp:13:60:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/offset_based_getter.hpp:63:3: error: static assertion failed: ====================> Boost.PFR: Member sequence does not indicate correct size for struct type!  
   static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!");  
   ^~~~~~~~~~~~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:29:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:24,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/offset_based_getter.hpp:64:3: error: static assertion failed: ====================> Boost.PFR: Member sequence does not indicate correct alignment for struct type!  
   static_assert(alignof(U) == alignof(S), "====================> Boost.PFR: Member sequence does not indicate correct alignment for struct type!");  
   ^~~~~~~~~~~~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:24,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp: In instantiation of ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<I ...>) [with T = Message; F = boost::pfr::for_each_field(T&&, F&&) [with T = Message&; F = main()::<lambda(const auto:7&)>]::<lambda(auto:1&&)>; unsigned int ...I = {0u}; std::index_sequence<I ...> = std::integer_sequence<unsigned int, 0u>]’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:140:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = Message&; F = main()::<lambda(const auto:7&)>]’  
/tmp/untitled/main.cpp:13:60:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:254:23: error: use of ‘boost::pfr::for_each_field(T&&, F&&)::<lambda(auto:1&&)> mutable [with auto:1 = boost::pfr::detail::sequence_tuple::tuple<std::allocator<char>&>; T = Message&; F = main()::<lambda(const auto:7&)>]’ before deduction of ‘auto’  
     std::forward<F>(f)(  
     ~~~~~~~~~~~~~~~~~~^  
         boost::pfr::detail::tie_as_tuple_loophole_impl(t)  
         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
     );  
     ~                    
distcc[16882] ERROR: compile /tmp/untitled/main.cpp on localhost failed  
ninja: build stopped: subcommand failed.  
18:02:49: The process "/home/cucchetf/tools/cmake/bin/cmake" exited with code 1.  
Error while building/deploying project untitled (kit: Private SDK)  
The kit Private SDK has configuration issues which might be the root cause for this problem.  
When executing step "CMake Build"  
18:02:49: Elapsed time: 00:02.  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-23 20:33:36 UTC  
> Updated at: 2019-01-23 20:35:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/34#issuecomment-456955564  

This is an another issue within the C++ Standard (like in #33), but I have not localized it. `std::tuple` is over-complicated, It has too many constructors, and looks like one of the constructors misses `explicit`. Because of that `std::tuple` attempts to initialize from `std::allocatror`, and that leads to incorrect reflection. I've failed to find the proper fix/workaround for that in C++14.  
  
Switching to С++17 fixes this issue.  
Won't fix for loophole in C++14.
