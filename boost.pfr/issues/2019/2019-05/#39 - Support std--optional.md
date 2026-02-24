# #39 - Support std::optional<> [Closed]

> Username: filcuc  
> Created at: 2019-05-17 12:27:17 UTC  
> Updated at: 2020-08-20 15:10:48 UTC  
> Closed at: 2020-08-20 15:10:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/39  

It seems that std::experimental::optional (c++14) or std::optional(c++17) are not supported  
```  
n file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:29:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:24,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/BaseValueHandlers.h:6,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/StdValueHandlers.h:4,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/UserParser.h:4,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/SystemFunctionParser.h:10,  
                 from /home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.h:5,  
                 from /home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.cpp:1:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/offset_based_getter.hpp: In instantiation of ‘class boost::pfr::detail::offset_based_getter<ipconnector::Temp, boost::pfr::detail::sequence_tuple::tuple<int, int> >’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:154:61:   required from ‘auto boost::pfr::detail::tie_as_tuple_loophole_impl(T&) [with T = ipconnector::Temp]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:255:55:   required from ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<I ...>) [with T = ipconnector::Temp; F = boost::pfr::for_each_field(T&&, F&&) [with T = ipconnector::Temp&; F = ipconnector::SFClientIPC::ModifyScene(const VimarIPCParser::SystemFunctionParser::ModifySceneRequest&)::<lambda(const auto:9&)>]::<lambda(auto:1&&)>; unsigned int ...I = {0u, 1u}; std::index_sequence<I ...> = std::integer_sequence<unsigned int, 0u, 1u>]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:140:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = ipconnector::Temp&; F = ipconnector::SFClientIPC::ModifyScene(const VimarIPCParser::SystemFunctionParser::ModifySceneRequest&)::<lambda(const auto:9&)>]’  
/home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.cpp:117:56:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/offset_based_getter.hpp:63:3: error: static assertion failed: ====================> Boost.PFR: Member sequence does not indicate correct size for struct type!  
   static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!");  
   ^~~~~~~~~~~~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:24,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/BaseValueHandlers.h:6,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/StdValueHandlers.h:4,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/UserParser.h:4,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/LibVimarIPCParser/SystemFunctionParser.h:10,  
                 from /home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.h:5,  
                 from /home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.cpp:1:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp: In instantiation of ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<I ...>) [with T = ipconnector::Temp; F = boost::pfr::for_each_field(T&&, F&&) [with T = ipconnector::Temp&; F = ipconnector::SFClientIPC::ModifyScene(const VimarIPCParser::SystemFunctionParser::ModifySceneRequest&)::<lambda(const auto:9&)>]::<lambda(auto:1&&)>; unsigned int ...I = {0u, 1u}; std::index_sequence<I ...> = std::integer_sequence<unsigned int, 0u, 1u>]’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:140:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = ipconnector::Temp&; F = ipconnector::SFClientIPC::ModifyScene(const VimarIPCParser::SystemFunctionParser::ModifySceneRequest&)::<lambda(const auto:9&)>]’  
/home/cucchetf/workspace/p378_ipconnectorserver/src/ipc/SFClientIPC.cpp:117:56:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:254:23: error: use of ‘boost::pfr::for_each_field(T&&, F&&)::<lambda(auto:1&&)> mutable [with auto:1 = boost::pfr::detail::sequence_tuple::tuple<int&, int&>; T = ipconnector::Temp&; F = ipconnector::SFClientIPC::ModifyScene(const VimarIPCParser::SystemFunctionParser::ModifySceneRequest&)::<lambda(const auto:9&)>]’ before deduction of ‘auto’  
     std::forward<F>(f)(  
     ~~~~~~~~~~~~~~~~~~^  
         boost::pfr::detail::tie_as_tuple_loophole_impl(t)  
         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
     );  
     ~                    
```

---

## Comment 1

> Username: filcuc  
> Created at: 2019-05-17 12:30:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/39#issuecomment-493436193  

arm-poky-linux-gnueabi-g++ (GCC) 6.3.0  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

---

## Comment 2

> Username: filcuc  
> Created at: 2019-05-17 12:32:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/39#issuecomment-493436662  

```  
#include <iostream>  
#include <boost/pfr.hpp>  
#include <experimental/optional>  
  
struct Test {  
  std::experimental::optional<int> value;  
};  
  
int main() {  
  Test test;  
  boost::pfr::for_each_field(test, [](const auto& v) {});  
  return 0;  
}  
```

---

## Comment 3

> Username: Stannieman  
> Created at: 2020-04-08 14:24:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/39#issuecomment-610990027  

Instead of supporting std::optional specifically, would it be possible to enable a more generic extension of the serialization mechanism?  
  
For example we can register a function to handle some specific type (optional<T>, vector<T>, ...) which returns a string, and then if a field of such type is encountered it will call that function instead of failing.

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-08-20 15:09:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/39#issuecomment-677723894  

> Instead of supporting std::optional specifically, would it be possible to enable a more generic extension of the serialization mechanism?  
  
Unfortunately that won't help with the `optional` case case.

---

## Comment 5

> Username: apolukhin  
> Created at: 2020-08-20 15:10:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/39#issuecomment-677724797  

The only way to deal with the issue is to upgrade to the C++17 standard.  
  
I've described the limitations of C++14 more precisely in the docs: http://apolukhin.github.io/magic_get/boost_pfr/limitations.html  
  
Alas, that's the only known solution to the problem.
