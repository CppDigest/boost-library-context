# #30 - Reopen #27 [Closed]

> Username: filcuc  
> Created at: 2018-11-22 10:05:34 UTC  
> Updated at: 2019-01-06 11:07:47 UTC  
> Closed at: 2019-01-05 10:42:29 UTC  
> Url: https://github.com/boostorg/pfr/issues/30  

Issue #27 should have been fixed in latest master but it doesn't.  
  
Compiling   
```  
#include <memory>  
#include <boost/pfr.hpp>  
  
struct Message {  
  std::unique_ptr<int> data;  
};  
  
int main(int argc, char* argv[]) {  
  Message message;  
  auto& ptr = boost::pfr::get<0>(message);  
  return 0;  
}  
```  
  
gives me  
```  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:23,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp: In instantiation of ‘struct boost::pfr::detail::loophole_type_list<Message, std::integer_sequence<unsigned int, 0u> >’:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:108:72:   required from ‘auto boost::pfr::detail::tie_as_tuple_loophole_impl(T&) [with T = Message]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:199:58:   required from ‘auto boost::pfr::detail::tie_as_tuple(T&) [with T = Message]’  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:50:64:   required from ‘constexpr decltype(auto) boost::pfr::get(T&) [with unsigned int I = 0u; T = Message]’  
/tmp/untitled/main.cpp:10:41:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:98:68: error: use of deleted function ‘std::unique_ptr<_Tp, _Dp>::unique_ptr(const std::unique_ptr<_Tp, _Dp>&) [with _Tp = int; _Dp = std::default_delete<int>]’  
     : sequence_tuple::tuple< decltype(T{ loophole_ubiq<T, I>{}... }, 0) >  
                                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/memory:81:0,  
                 from /tmp/untitled/main.cpp:1:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/c++/6.3.0/bits/unique_ptr.h:359:7: note: declared here  
       unique_ptr(const unique_ptr&) = delete;  
       ^~~~~~~~~~  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14.hpp:13:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:23,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:87:15: note:   after user-defined conversion: constexpr boost::pfr::detail::loophole_ubiq<T, N>::operator U&() const [with U = std::unique_ptr<int>; unsigned int <anonymous> = 1u; T = Message; unsigned int N = 0u]  
     constexpr operator U&() const noexcept; // `const` here helps to avoid ambiguity in loophole instantiations. optional_like test validate that behavior.  
               ^~~~~~~~  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:100:58: error: use of ‘auto boost::pfr::detail::loophole(boost::pfr::detail::tag<Message, 0u>)’ before deduction of ‘auto’  
     using type = sequence_tuple::tuple< decltype(loophole(tag<T, I>{}))... >;  
                                                  ~~~~~~~~^~~~~~~~~~~~~  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/detail/core14_loophole.hpp:100:58: error: use of ‘auto boost::pfr::detail::loophole(boost::pfr::detail::tag<Message, 0u>)’ before deduction of ‘auto’  
In file included from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise.hpp:12:0,  
                 from /opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr.hpp:12,  
                 from /tmp/untitled/main.cpp:2:  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp: In instantiation of ‘constexpr decltype(auto) boost::pfr::get(T&) [with unsigned int I = 0u; T = Message]’:  
/tmp/untitled/main.cpp:10:41:   required from here  
/opt/vrag/releases/private/cucchetf-vimar-agx-dbg-latest/sdk/sysroots/cortexa9hf-neon-poky-linux-gnueabi/usr/include/boost/pfr/precise/core.hpp:50:42: error: invalid use of void expression  
     return detail::sequence_tuple::get<I>( detail::tie_as_tuple(val) );  
            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/untitled/main.cpp: In function ‘int main(int, char**)’:  
/tmp/untitled/main.cpp:10:41: error: forming reference to void  
   auto& ptr = boost::pfr::get<0>(message);  
                                         ^  
make[2]: *** [CMakeFiles/untitled.dir/main.cpp.o] Error 1  
make[1]: *** [CMakeFiles/untitled.dir/all] Error 2  
```

---

## Comment 1

> Username: filcuc  
> Created at: 2018-11-22 10:06:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-440978487  

Compiler  
```  
arm-poky-linux-gnueabi-gcc (GCC) 6.3.0  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
```

---

## Comment 2

> Username: filcuc  
> Created at: 2018-11-26 15:44:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-441685443  

@apolukhin is this expected?

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-11-26 19:38:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-441770157  

Have not investigated yet. Need some time, but from the first glance it does not look right.

---

## Comment 4

> Username: filcuc  
> Created at: 2018-11-26 20:23:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-441784769  

@apolukhin basically the loophole tries to invoke unique_ptr copy constructor.  
That said the same error is reproducible also before #27 was (marked) fixed by uncommenting the lines you pointed out on #27 (that should have enabled reflection for move only types).

---

## Comment 5

> Username: apolukhin  
> Created at: 2019-01-06 06:52:21 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-451720072  

Manny thanks for the report.  
  
Now everything should work.

---

## Comment 6

> Username: filcuc  
> Created at: 2019-01-06 11:07:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/30#issuecomment-451733051  

@apolukhin well thank you for fixing it:D i'll try a new build next week and report back
