# #301 - build failed with cxxflags -D BOOST_FIBERS_NO_ATOMICS (version 1.80.0) [Open]

> Username: rockeet  
> Created at: 2022-08-24 13:11:08 UTC  
> Updated at: 2022-09-16 06:41:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/301  

## Build failed with commands:  
```bash  
bash bootstrap.sh  
./b2 cxxflags="-fPIC -std=gnu++17 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS" \  
     cflags="-fPIC" link=shared variant=debug \  
     --with-fiber --with-context --with-system  
```  
The fail is caused by added `cxxflags` `-DBOOST_FIBERS_NO_ATOMICS`.  
  
boost version: `1.80.0` from boost.org [download](https://boostorg.jfrog.io/artifactory/main/release/1.80.0/source/boost_1_80_0.tar.gz).  
  
-----  
## Build error details:  
```  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/waker.o  
In file included from libs/fiber/src/waker.cpp:2:  
./boost/fiber/waker.hpp:79:39: error: 'std::chrono' has not been declared  
   79 |                                  std::chrono::steady_clock::time_point const&);  
      |                                       ^~~~~~  
./boost/fiber/waker.hpp:79:72: error: expected ',' or '...' before 'const'  
   79 |                                  std::chrono::steady_clock::time_point const&);  
      |                                                                        ^~~~~  
In file included from libs/fiber/src/waker.cpp:3:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
libs/fiber/src/waker.cpp: At global scope:  
libs/fiber/src/waker.cpp:26:1: error: no declaration matches 'bool boost::fibers::wait_queue::suspend_and_wait_until(boost::fibers::detail::spinlock_lock&, boost::fibers::context*, const std::chrono::_V2::steady_clock::time_point&)'  
   26 | wait_queue::suspend_and_wait_until( detail::spinlock_lock & lk,  
      | ^~~~~~~~~~  
./boost/fiber/waker.hpp:77:10: note: candidate is: 'bool boost::fibers::wait_queue::suspend_and_wait_until(boost::fibers::detail::spinlock_lock&, boost::fibers::context*, int)'  
   77 |     bool suspend_and_wait_until( detail::spinlock_lock &,  
      |          ^~~~~~~~~~~~~~~~~~~~~~  
./boost/fiber/waker.hpp:71:25: note: 'class boost::fibers::wait_queue' defined here  
   71 | class BOOST_FIBERS_DECL wait_queue {  
      |                         ^~~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/waker.o" "libs/fiber/src/waker.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/waker.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/algorithm.o  
In file included from libs/fiber/src/algo/algorithm.cpp:9:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/algorithm.o" "libs/fiber/src/algo/algorithm.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/algorithm.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/mutex.o  
In file included from ./boost/fiber/mutex.hpp:14,  
                 from libs/fiber/src/mutex.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/mutex.cpp:14:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/mutex.o" "libs/fiber/src/mutex.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/mutex.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/timed_mutex.o  
In file included from ./boost/fiber/timed_mutex.hpp:15,  
                 from libs/fiber/src/timed_mutex.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/timed_mutex.cpp:13:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/timed_mutex.o" "libs/fiber/src/timed_mutex.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/timed_mutex.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/round_robin.o  
In file included from ./boost/fiber/algo/round_robin.hpp:16,  
                 from libs/fiber/src/algo/round_robin.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from ./boost/fiber/algo/round_robin.hpp:18:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/round_robin.o" "libs/fiber/src/algo/round_robin.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/round_robin.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/fiber.o  
In file included from ./boost/fiber/fiber.hpp:22,  
                 from libs/fiber/src/fiber.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/fiber.cpp:14:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/fiber.o" "libs/fiber/src/fiber.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/fiber.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/properties.o  
In file included from ./boost/fiber/scheduler.hpp:23,  
                 from libs/fiber/src/properties.cpp:11:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/properties.o" "libs/fiber/src/properties.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/properties.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/scheduler.o  
In file included from ./boost/fiber/scheduler.hpp:23,  
                 from libs/fiber/src/scheduler.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/scheduler.o" "libs/fiber/src/scheduler.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/scheduler.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/condition_variable.o  
In file included from ./boost/fiber/condition_variable.hpp:20,  
                 from libs/fiber/src/condition_variable.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from ./boost/fiber/operations.hpp:18,  
                 from ./boost/fiber/condition_variable.hpp:26:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/condition_variable.o" "libs/fiber/src/condition_variable.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/condition_variable.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_timed_mutex.o  
In file included from ./boost/fiber/recursive_timed_mutex.hpp:19,  
                 from libs/fiber/src/recursive_timed_mutex.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/recursive_timed_mutex.cpp:13:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_timed_mutex.o" "libs/fiber/src/recursive_timed_mutex.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_timed_mutex.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_mutex.o  
In file included from ./boost/fiber/recursive_mutex.hpp:18,  
                 from libs/fiber/src/recursive_mutex.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/recursive_mutex.cpp:13:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_mutex.o" "libs/fiber/src/recursive_mutex.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/recursive_mutex.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/shared_work.o  
In file included from ./boost/fiber/algo/shared_work.hpp:18,  
                 from libs/fiber/src/algo/shared_work.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from ./boost/fiber/algo/shared_work.hpp:20:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/shared_work.o" "libs/fiber/src/algo/shared_work.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/shared_work.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/barrier.o  
In file included from ./boost/fiber/condition_variable.hpp:20,  
                 from ./boost/fiber/barrier.hpp:14,  
                 from libs/fiber/src/barrier.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from ./boost/fiber/operations.hpp:18,  
                 from ./boost/fiber/condition_variable.hpp:26:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/barrier.o" "libs/fiber/src/barrier.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/barrier.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/context.o  
In file included from libs/fiber/src/context.cpp:7:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from libs/fiber/src/context.cpp:14:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
libs/fiber/src/context.cpp: In member function 'void boost::fibers::context::join()':  
libs/fiber/src/context.cpp:195:39: error: cannot convert 'std::unique_lock<boost::fibers::detail::spinlock>' to 'boost::fibers::detail::spinlock_lock&'  
  195 |         wait_queue_.suspend_and_wait( lk, active_ctx);  
      |                                       ^~  
      |                                       |  
      |                                       std::unique_lock<boost::fibers::detail::spinlock>  
In file included from ./boost/fiber/context.hpp:47:  
./boost/fiber/waker.hpp:76:28: note:   initializing argument 1 of 'void boost::fibers::wait_queue::suspend_and_wait(boost::fibers::detail::spinlock_lock&, boost::fibers::context*)'  
   76 |     void suspend_and_wait( detail::spinlock_lock &, context *);  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~  
libs/fiber/src/context.cpp: In member function 'boost::context::fiber boost::fibers::context::terminate()':  
libs/fiber/src/context.cpp:235:40: error: cannot convert 'std::unique_lock<boost::fibers::detail::spinlock>' to 'boost::fibers::detail::spinlock_lock&'  
  235 |     return get_scheduler()->terminate( lk, this);  
      |                                        ^~  
      |                                        |  
      |                                        std::unique_lock<boost::fibers::detail::spinlock>  
./boost/fiber/scheduler.hpp:127:38: note:   initializing argument 1 of 'boost::context::fiber boost::fibers::scheduler::terminate(boost::fibers::detail::spinlock_lock&, boost::fibers::context*)'  
  127 |     boost::context::fiber terminate( detail::spinlock_lock &, context *) noexcept;  
      |                                      ^~~~~~~~~~~~~~~~~~~~~~~  
libs/fiber/src/context.cpp: In member function 'bool boost::fibers::context::wake(size_t)':  
libs/fiber/src/context.cpp:258:26: error: 'waker_epoch_' was not declared in this scope  
  258 |     bool is_last_waker = waker_epoch_.compare_exchange_strong(expected, epoch + 1, std::memory_order_acq_rel);  
      |                          ^~~~~~~~~~~~  
libs/fiber/src/context.cpp:269:26: error: 'class boost::fibers::scheduler' has no member named 'schedule_from_remote'  
  269 |         get_scheduler()->schedule_from_remote( this);  
      |                          ^~~~~~~~~~~~~~~~~~~~  
libs/fiber/src/context.cpp: In member function 'bool boost::fibers::context::remote_ready_is_linked() const':  
libs/fiber/src/context.cpp:348:12: error: 'remote_ready_hook_' was not declared in this scope; did you mean 'remote_ready_link'?  
  348 |     return remote_ready_hook_.is_linked();  
      |            ^~~~~~~~~~~~~~~~~~  
      |            remote_ready_link  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/context.o" "libs/fiber/src/context.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/context.o...  
gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/work_stealing.o  
In file included from ./boost/fiber/algo/work_stealing.hpp:23,  
                 from libs/fiber/src/algo/work_stealing.cpp:8:  
./boost/fiber/context.hpp: In member function 'boost::fibers::waker boost::fibers::context::create_waker()':  
./boost/fiber/context.hpp:284:26: error: 'waker_epoch_' was not declared in this scope  
  284 |         return { this, ++waker_epoch_ };  
      |                          ^~~~~~~~~~~~  
./boost/fiber/context.hpp:284:39: error: could not convert '{((boost::fibers::context*)this), <expression error>}' from '<brace-enclosed initializer list>' to 'boost::fibers::waker'  
  284 |         return { this, ++waker_epoch_ };  
      |                                       ^  
      |                                       |  
      |                                       <brace-enclosed initializer list>  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_add_ref(context*)':  
./boost/fiber/context.hpp:370:25: error: request for member 'fetch_add' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  370 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
      |                         ^~~~~~~~~  
./boost/fiber/context.hpp: In function 'void boost::fibers::intrusive_ptr_release(context*)':  
./boost/fiber/context.hpp:375:35: error: request for member 'fetch_sub' in 'ctx->boost::fibers::context::use_count_', which is of non-class type 'std::size_t' {aka 'long unsigned int'}  
  375 |         if ( 1 == ctx->use_count_.fetch_sub( 1, std::memory_order_release) ) {  
      |                                   ^~~~~~~~~  
In file included from ./boost/fiber/algo/work_stealing.hpp:27:  
./boost/fiber/scheduler.hpp: At global scope:  
./boost/fiber/scheduler.hpp:78:68: error: 'remote_ready_hook_' is not a member of 'boost::fibers::context'  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                    ^~~~~~~~~~~~~~~~~~  
./boost/fiber/scheduler.hpp:78:87: error: template argument 3 is invalid  
   78 |                     context, detail::remote_ready_hook, & context::remote_ready_hook_ >,  
      |                                                                                       ^  
./boost/fiber/scheduler.hpp:81:13: error: template argument 2 is invalid  
   81 |             >                                               remote_ready_queue_type;  
      |             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fPIC -std=gnu++17 -g3 -ftls-model=initial-exec -DBOOST_FIBERS_NO_ATOMICS -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I"."  -c -o "bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/work_stealing.o" "libs/fiber/src/algo/work_stealing.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden/algo/work_stealing.o...  
...skipped <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>libboost_fiber.so.1.80.0 for lack of <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>algo/algorithm.o...  
...skipped <p/hddpool/pxe/node-shared/leipeng/osc/boost_1_80_0/stage/lib>libboost_fiber.so.1.80.0 for lack of <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>libboost_fiber.so.1.80.0...  
...skipped <p/hddpool/pxe/node-shared/leipeng/osc/boost_1_80_0/stage/lib>libboost_fiber.so for lack of <p/hddpool/pxe/node-shared/leipeng/osc/boost_1_80_0/stage/lib>libboost_fiber.so.1.80.0...  
...skipped <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>libboost_fiber-variant-shared.cmake for lack of <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>libboost_fiber.so.1.80.0...  
...skipped <p/hddpool/pxe/node-shared/leipeng/osc/boost_1_80_0/stage/lib/cmake/boost_fiber-1.80.0>libboost_fiber-variant-shared.cmake for lack of <pbin.v2/libs/fiber/build/gcc-12/debug/threading-multi/visibility-hidden>libboost_fiber-variant-shared.cmake...  
...failed updating 15 targets...  
...skipped 5 targets...  
  
```
