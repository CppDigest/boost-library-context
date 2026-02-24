# #56 - boost 1.64.0: Boost.Context fails to build -> Call of overloaded 'callcc(...) is ambiguous' [Closed]

> Username: ClaymorePT  
> Created at: 2017-04-24 10:41:03 UTC  
> Updated at: 2017-04-24 11:51:25 UTC  
> Closed at: 2017-04-24 11:15:00 UTC  
> Url: https://github.com/boostorg/context/issues/56  

I was trying to compile Boost 1.64.0 for OpenWRT when the build failed for Arm Cortex A7 (Raspberry Pi 2) using Gcc 5.2 with Musl 1.1.16.  
  
The build output with the error follows.  
  
Any ideas on what it could be ?  
  
```  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-arm/release/abi-aapcs/boost.locale.iconv-on/boost.locale.posix-off/link-static/threading-multi/context.o  
libs/fiber/src/context.cpp: In constructor 'boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)':  
libs/fiber/src/context.cpp:236:14: error: call of overloaded 'callcc(const std::allocator_arg_t&, const boost::context::preallocated&, const default_stack&, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>)' is ambiguous  
             });  
              ^  
In file included from ./boost/fiber/context.hpp:28:0,  
                 from libs/fiber/src/context.cpp:7:  
./boost/context/continuation.hpp:469:1: note: candidate: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = const boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>}]  
 callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
./boost/context/continuation.hpp:483:1: note: candidate: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>; Arg = {}]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
./boost/context/continuation.hpp:514:1: note: candidate: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
 ^  
./boost/context/continuation.hpp:457:1: note: candidate: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>}; <template-parameter-1-3> = void]  
 callcc( Fn && fn, Arg ... arg) {  
 ^  
  
```

---

## Comment 1

> Username: olk  
> Created at: 2017-04-24 11:15:00 UTC  
> Url: https://github.com/boostorg/context/issues/56#issuecomment-296622863  

duplicate of #52

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2017-04-24 11:51:12 UTC  
> Updated at: 2017-04-24 11:51:25 UTC  
> Url: https://github.com/boostorg/context/issues/56#issuecomment-296635159  

Applying commit https://github.com/boostorg/context/pull/54/commits/e66b64c1ec0bcc496d3d9212d66959351a5af551 also fixed the issue for GCC 5.2  
  
Thank you @Lastique
