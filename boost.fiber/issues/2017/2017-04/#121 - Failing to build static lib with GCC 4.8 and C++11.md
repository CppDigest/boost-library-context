# #121 - Failing to build static lib with GCC 4.8 and C++11 [Closed]

> Username: DenizThatMenace  
> Created at: 2017-04-20 09:18:13 UTC  
> Updated at: 2017-04-30 11:52:24 UTC  
> Closed at: 2017-04-24 06:54:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/121  

Compiling the newest Boost release (version **1.64.0**) fails when trying to compile *Boost.Fiber* as **static library** with **GCC 4.8** and `-std=c++11`.  
  
This seems to be related to [this issue](https://github.com/boostorg/context/issues/52) with *Boost.Context*. The call to `callcc` is an ambiguous overload.  
  
```  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi  
common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/algorithm.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/round_robin.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/shared_work.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/work_stealing.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/barrier.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/condition_variable.o  
gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o  
libs/fiber/src/context.cpp: In constructor ‘boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)’:  
libs/fiber/src/context.cpp:236:14: error: call of overloaded ‘callcc(const std::allocator_arg_t&, const boost::context::preallocated&, const default_stack&, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5)’ is ambiguous  
             });  
              ^  
libs/fiber/src/context.cpp:236:14: note: candidates are:  
In file included from ./boost/fiber/context.hpp:28:0,  
                 from libs/fiber/src/context.cpp:7:  
./boost/context/continuation.hpp:469:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = const boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}]  
 callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
./boost/context/continuation.hpp:483:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5; Arg = {}]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
 ^  
./boost/context/continuation.hpp:514:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5]  
 callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
 ^  
./boost/context/continuation.hpp:457:1: note: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}; <template-parameter-1-3> = void]  
 callcc( Fn && fn, Arg ... arg) {  
 ^  
  
    "g++-4.8"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -pthread -m64 -m64 -fpic  -std=c++11   -m64 -fpic    -DBOOST_ALL_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FIBERS_SOURCE -DNDEBUG  -I"." -c -o "/home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o" "libs/fiber/src/context.cpp"  
  
...failed gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o...  
```  
  
HTH,  
Deniz Bahadir  
  
PS: I reported this issue already for the beta releases of *Boost 1.64.0* on the Boost mailing-list but it obviously got not much attention.

---

## Comment 1

> Username: olk  
> Created at: 2017-04-20 09:37:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-295653175  

problem of gcc-4.8/9  
  
Am 20.04.2017 11:18 vorm. schrieb "Deniz Bahadir" <notifications@github.com  
>:  
  
> Compiling the newest Boost release (version *1.64.0*) fails when trying  
> to compile *Boost.Fiber* as *static library* with *GCC 4.8* and -std=c++11  
> .  
>  
> This seems to be related to this issue  
> <https://github.com/boostorg/context/issues/52> with *Boost.Context*. The  
> call to callcc is an ambiguous overload.  
>  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/algorithm.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/round_robin.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/shared_work.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/work_stealing.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/barrier.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/condition_variable.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o  
> libs/fiber/src/context.cpp: In constructor ‘boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)’:  
> libs/fiber/src/context.cpp:236:14: error: call of overloaded ‘callcc(const std::allocator_arg_t&, const boost::context::preallocated&, const default_stack&, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5)’ is ambiguous  
>              });  
>               ^  
> libs/fiber/src/context.cpp:236:14: note: candidates are:  
> In file included from ./boost/fiber/context.hpp:28:0,  
>                  from libs/fiber/src/context.cpp:7:  
> ./boost/context/continuation.hpp:469:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = const boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}]  
>  callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
>  ^  
> ./boost/context/continuation.hpp:483:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5; Arg = {}]  
>  callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
>  ^  
> ./boost/context/continuation.hpp:514:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5]  
>  callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
>  ^  
> ./boost/context/continuation.hpp:457:1: note: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}; <template-parameter-1-3> = void]  
>  callcc( Fn && fn, Arg ... arg) {  
>  ^  
>  
>     "g++-4.8"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -pthread -m64 -m64 -fpic  -std=c++11   -m64 -fpic    -DBOOST_ALL_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FIBERS_SOURCE -DNDEBUG  -I"." -c -o "/home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o" "libs/fiber/src/context.cpp"  
>  
> ...failed gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o...  
>  
> HTH,  
> Deniz Bahadir  
>  
> PS: I reported this issue already for the beta releases of *Boost 1.64.0*  
> on the Boost mailing-list but it obviously got not much attention.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/121>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QD3_Eh8oIQ0sGX9aww3144mrtVhGks5rxyLWgaJpZM4NCwgC>  
> .  
>

---

## Comment 2

> Username: olk  
> Created at: 2017-04-20 09:41:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-295654364  

I mean it's a bug of gcc-4.8/4.9 - the code compiles with gcc-5/6, clang ,  
intel and msvc  
  
Am 20.04.2017 11:37 vorm. schrieb "Oliver Kowalke" <oliver.kowalke@gmail.com  
>:  
  
problem of gcc-4.8/9  
  
Am 20.04.2017 11:18 vorm. schrieb "Deniz Bahadir" <notifications@github.com  
>:  
  
> Compiling the newest Boost release (version *1.64.0*) fails when trying  
> to compile *Boost.Fiber* as *static library* with *GCC 4.8* and -std=c++11  
> .  
>  
> This seems to be related to this issue  
> <https://github.com/boostorg/context/issues/52> with *Boost.Context*. The  
> call to callcc is an ambiguous overload.  
>  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi  
> common.mkdir /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/algorithm.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/round_robin.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/shared_work.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/algo/work_stealing.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/barrier.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/condition_variable.o  
> gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o  
> libs/fiber/src/context.cpp: In constructor ‘boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)’:  
> libs/fiber/src/context.cpp:236:14: error: call of overloaded ‘callcc(const std::allocator_arg_t&, const boost::context::preallocated&, const default_stack&, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5)’ is ambiguous  
>              });  
>               ^  
> libs/fiber/src/context.cpp:236:14: note: candidates are:  
> In file included from ./boost/fiber/context.hpp:28:0,  
>                  from libs/fiber/src/context.cpp:7:  
> ./boost/context/continuation.hpp:469:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::preallocated; Fn = const boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Arg = {boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}]  
>  callcc( std::allocator_arg_t, StackAlloc salloc, Fn && fn, Arg ... arg) {  
>  ^  
> ./boost/context/continuation.hpp:483:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&, Arg ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5; Arg = {}]  
>  callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn, Arg ... arg) {  
>  ^  
> ./boost/context/continuation.hpp:514:1: note: boost::context::continuation boost::context::callcc(std::allocator_arg_t, boost::context::preallocated, StackAlloc, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5]  
>  callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
>  ^  
> ./boost/context/continuation.hpp:457:1: note: boost::context::continuation boost::context::callcc(Fn&&, Arg ...) [with Fn = const std::allocator_arg_t&; Arg = {boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::__lambda5}; <template-parameter-1-3> = void]  
>  callcc( Fn && fn, Arg ... arg) {  
>  ^  
>  
>     "g++-4.8"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -pthread -m64 -m64 -fpic  -std=c++11   -m64 -fpic    -DBOOST_ALL_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FIBERS_SOURCE -DNDEBUG  -I"." -c -o "/home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o" "libs/fiber/src/context.cpp"  
>  
> ...failed gcc.compile.c++ /home/jenkins/workspace/TEST_Boost_Build/gcc48/build/boost/boost/bin.v2/libs/fiber/build/gcc-4.8/release/link-static/threading-multi/context.o...  
>  
> HTH,  
> Deniz Bahadir  
>  
> PS: I reported this issue already for the beta releases of *Boost 1.64.0*  
> on the Boost mailing-list but it obviously got not much attention.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/121>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QD3_Eh8oIQ0sGX9aww3144mrtVhGks5rxyLWgaJpZM4NCwgC>  
> .  
>

---

## Comment 3

> Username: DenizThatMenace  
> Created at: 2017-04-20 11:48:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-295702489  

Mmhhh... I see.  
  
You could provide a workaround by adding copies of the original `callcc` function-templates to **Boost.Context** with different names (e.g. `callcc_no_args` `callcc_with_args` `callcc_no_args_palloc` `callcc_with_args_palloc`) which are only available if *GCC 4.8/4.9* are used. You then have to make sure to call these specific functions from **Boost.Fiber** if either GCC 4.8 or 4.9 is used.  
  
Of course this is kind of ugly but might help in this case.  
  
I tried it out and it works. See the attached patches (against the Boost 1.64.0 release).  
  
[context__include__continuation.hpp.diff.txt](https://github.com/boostorg/fiber/files/942740/context__include__continuation.hpp.diff.txt)  
[fiber__src__context.cpp.diff.txt](https://github.com/boostorg/fiber/files/942739/fiber__src__context.cpp.diff.txt)

---

## Comment 4

> Username: DaoWen  
> Created at: 2017-04-21 05:28:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296073350  

I was able to build Boost.Fiber with `toolset=gcc-4.9` and run all tests successfully using the patches in PR #122 and PR boostorg/context#55. Using `toolset=gcc-4.8`, a few tests failed, but most passed.  
  
Would you be able to verify?

---

## Comment 5

> Username: DenizThatMenace  
> Created at: 2017-04-21 14:16:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296202568  

I can confirm that **Boost.Fiber** from Boost releases 1.64.0 with these patches **compiles fine** with **GCC 4.8**.  
  
I assume that during compilation with `b2` all tests were compiled and run automatically. **And none of the tests failed!** :+1:  
  
(However, I just tried to compile *Boost.Fiber* and no other Boost-library which depends on it or *Boost.Context*.)

---

## Comment 6

> Username: DaoWen  
> Created at: 2017-04-21 16:50:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296244213  

> I assume that during compilation with b2 all tests were compiled and run automatically.  
  
I actually don't think the tests are run unless you explicitly run them. I'm fairly new to the Boost build toolchain, so there might be better ways to do this, but here's how I run the tests for a specific module:  
  
    ./b2 toolset=gcc-4.9 cxxflags="-std=c++11" --build-dir="/tmp/nick/boost/1.64" libs/fiber/test  
  
If it says anything about "skipped" or "failed" targets at the end of the run, then that indicates a problem.

---

## Comment 7

> Username: DenizThatMenace  
> Created at: 2017-04-21 20:59:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296304257  

OK, you are right. I somehow thought that the unit-tests would have been run automatically. (Maybe that is the case for some other Boost-libraries?)  
  
One unit-test failed and a second was skipped because of this, when compiling with GCC 4.8. GCC 6 compiles fine instead (while Clang 3.8 does only compile the library but does not even try to compile the libraries).  
  
So I can confirm your findings.  
  
The failure seems to be due to the unit-test somehow trying to use the deleted copy-constructor of a move-only type (`std::atomic`). Maybe just the unit-test is not entirely correct (for the standard-library of GCC 4.8) and needs to be fixed (or a workaround)?

---

## Comment 8

> Username: DaoWen  
> Created at: 2017-04-21 21:17:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296307710  

> The failure seems to be due to the unit-test somehow trying to use the deleted copy-constructor of a move-only type (std::atomic).  
  
Yes, that's the same error I observed.  
  
> Maybe just the unit-test is not entirely correct (for the standard-library of GCC 4.8) and needs to be fixed (or a workaround)?  
  
I'm guessing that this is actually an issue/deficiency in the C++ standard library that ships with GCC 4.8, which means the boost library can't really work around it (aside from maybe documenting the limitation and suggesting to upgrade your compiler toolchain). But, the good news is that *everything else* worked, so as long as you don't try to copy something with a deleted copy constructor into a Boost.Fiber promise, then I think you should be fine using GCC 4.8.

---

## Comment 9

> Username: olk  
> Created at: 2017-04-24 06:54:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-296545355  

should be fixed now (not the atomic ctor issue), ty

---

## Comment 10

> Username: quangbuule  
> Created at: 2017-04-30 00:52:33 UTC  
> Updated at: 2017-04-30 11:52:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/121#issuecomment-298203795  

I think this problem is not solved entirely (maybe related to gcc 4.9), here is my build error (branch develop):  
```  
libs/fiber/src/context.cpp: In constructor ‘boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)’:  
libs/fiber/src/context.cpp:236:14: error: call of overloaded ‘callcc(const std::allocator_arg_t&, const boost::context::preallocated&, const default_stack&, boost::fibers::context::context(boost::fibers::dispatcher_context_t, const boost::context::preallocated&, const default_stack&, boost::fibers::scheduler*)::<lambda(boost::context::continuation&&)>)’ is ambiguous  
             });  
              ^  
libs/fiber/src/context.cpp:236:14: note: candidates are:  
In file included from ./boost/fiber/context.hpp:28:0,  
                 from libs/fiber/src/context.cpp:7:  
```  
  
----  
Updated: I fixed by checking out the boost_context to develop branch too.
