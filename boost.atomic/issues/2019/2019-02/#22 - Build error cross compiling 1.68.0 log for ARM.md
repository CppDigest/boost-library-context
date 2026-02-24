# #22 - Build error cross compiling 1.68.0 log for ARM [Closed]

> Username: DrMaxP  
> Created at: 2019-02-22 17:25:41 UTC  
> Updated at: 2019-02-25 09:30:20 UTC  
> Closed at: 2019-02-25 09:30:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/22  

I'm attempting to cross compile v1.68 for ARM on Ubuntu (Cosmic), based on checking out that version of the code. I am unable to build some of the libraries I need. For example, I need boost log, but this fails to build.   
  
I've set the compiler up in project.config.jam...  
  
if ! gcc in [ feature.values <toolset> ]  
{  
    using gcc : arm : /usr/bin/aarch64-linux-gnu-g++ ;  
}  
  
Running /b2 as follows gives errors:  
./b2 install -a toolset=gcc-arm architecture=arm abi=aapcs address-model=64 --prefix=/opt/cross/boost --with-log  
  
```  
....  
  
gcc.compile.c++ bin.v2/libs/atomic/build/gcc-arm/release/threading-multi/lockpool.o  
In file included from ./boost/atomic/detail/operations_lockfree.hpp:21:0,  
                 from libs/atomic/src/lockpool.cpp:22:  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:217:74: error: wrong number of template arguments (2, should be 1)  
     public gcc_atomic_operations< typename make_storage_type< 8u, Signed >::type >  
                                                                          ^  
In file included from /usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:19:0,  
                 from ./boost/atomic/detail/operations_lockfree.hpp:21,  
                 from libs/atomic/src/lockpool.cpp:22:  
./boost/atomic/detail/storage_type.hpp:66:8: note: provided for ‘template<long unsigned int Size> struct boost::atomics::detail::make_storage_type’  
 struct make_storage_type  
        ^  
In file included from ./boost/atomic/detail/operations_lockfree.hpp:21:0,  
                 from libs/atomic/src/lockpool.cpp:22:  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:217:82: error: template argument 1 is invalid  
     public gcc_atomic_operations< typename make_storage_type< 8u, Signed >::type >  
                                                                                  ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:218:1: error: expected ‘::’ before ‘{’ token  
 {  
 ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:218:1: error: expected class-name before ‘{’ token  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:255:74: error: wrong number of template arguments (2, should be 1)  
     public gcc_atomic_operations< typename make_storage_type< 4u, Signed >::type >  
                                                                          ^  
In file included from /usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:19:0,  
                 from ./boost/atomic/detail/operations_lockfree.hpp:21,  
                 from libs/atomic/src/lockpool.cpp:22:  
./boost/atomic/detail/storage_type.hpp:66:8: note: provided for ‘template<long unsigned int Size> struct boost::atomics::detail::make_storage_type’  
 struct make_storage_type  
        ^  
In file included from ./boost/atomic/detail/operations_lockfree.hpp:21:0,  
                 from libs/atomic/src/lockpool.cpp:22:  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:255:82: error: template argument 1 is invalid  
     public gcc_atomic_operations< typename make_storage_type< 4u, Signed >::type >  
                                                                                  ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:256:1: error: expected ‘::’ before ‘{’ token  
 {  
 ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:256:1: error: expected class-name before ‘{’ token  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:301:74: error: wrong number of template arguments (2, should be 1)  
     public gcc_atomic_operations< typename make_storage_type< 2u, Signed >::type >  
                                                                          ^  
In file included from /usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:19:0,  
                 from ./boost/atomic/detail/operations_lockfree.hpp:21,  
                 from libs/atomic/src/lockpool.cpp:22:  
./boost/atomic/detail/storage_type.hpp:66:8: note: provided for ‘template<long unsigned int Size> struct boost::atomics::detail::make_storage_type’  
 struct make_storage_type  
        ^  
In file included from ./boost/atomic/detail/operations_lockfree.hpp:21:0,  
                 from libs/atomic/src/lockpool.cpp:22:  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:301:82: error: template argument 1 is invalid  
     public gcc_atomic_operations< typename make_storage_type< 2u, Signed >::type >  
                                                                                  ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:302:1: error: expected ‘::’ before ‘{’ token  
 {  
 ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:302:1: error: expected class-name before ‘{’ token  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:355:74: error: wrong number of template arguments (2, should be 1)  
     public gcc_atomic_operations< typename make_storage_type< 1u, Signed >::type >  
                                                                          ^  
In file included from /usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:19:0,  
                 from ./boost/atomic/detail/operations_lockfree.hpp:21,  
                 from libs/atomic/src/lockpool.cpp:22:  
./boost/atomic/detail/storage_type.hpp:66:8: note: provided for ‘template<long unsigned int Size> struct boost::atomics::detail::make_storage_type’  
 struct make_storage_type  
        ^  
In file included from ./boost/atomic/detail/operations_lockfree.hpp:21:0,  
                 from libs/atomic/src/lockpool.cpp:22:  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:355:82: error: template argument 1 is invalid  
     public gcc_atomic_operations< typename make_storage_type< 1u, Signed >::type >  
                                                                                  ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:356:1: error: expected ‘::’ before ‘{’ token  
 {  
 ^  
/usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:356:1: error: expected class-name before ‘{’ token  
libs/atomic/src/lockpool.cpp:59:26: error: ‘storage_type’ in ‘boost::atomics::detail::{anonymous}::lock_operations {aka struct boost::atomics::detail::operations<1ul, false>}’ does not name a type  
 typedef lock_operations::storage_type lock_type;  
                          ^  
libs/atomic/src/lockpool.cpp:64:28: error: ‘lock_type’ was not declared in this scope  
     padding_size = (sizeof(lock_type) <= BOOST_ATOMIC_CACHE_LINE_SIZE ?  
                            ^  
libs/atomic/src/lockpool.cpp:65:48: error: ‘lock_type’ was not declared in this scope  
         (BOOST_ATOMIC_CACHE_LINE_SIZE - sizeof(lock_type)) :  
                                                ^  
libs/atomic/src/lockpool.cpp:66:48: error: ‘lock_type’ was not declared in this scope  
         (BOOST_ATOMIC_CACHE_LINE_SIZE - sizeof(lock_type) % BOOST_ATOMIC_CACHE_LINE_SIZE))  
                                                ^  
libs/atomic/src/lockpool.cpp:72:5: error: ‘lock_type’ does not name a type  
     lock_type lock;  
     ^  
libs/atomic/src/lockpool.cpp:80:5: error: ‘lock_type’ does not name a type  
     lock_type lock;  
     ^  
libs/atomic/src/lockpool.cpp: In constructor ‘boost::atomics::detail::lockpool::scoped_lock::scoped_lock(const volatile void*)’:  
libs/atomic/src/lockpool.cpp:109:111: error: ‘boost::atomics::detail::{anonymous}::padded_lock_t {aka struct boost::atomics::detail::{anonymous}::padded_lock<0u>}’ has no member named ‘lock’  
     m_lock(&g_lock_pool[reinterpret_cast< std::size_t >(addr) % (sizeof(g_lock_pool) / sizeof(*g_lock_pool))].lock)  
                                                                                                               ^  
libs/atomic/src/lockpool.cpp:111:12: error: ‘test_and_set’ is not a member of ‘boost::atomics::detail::{anonymous}::lock_operations {aka boost::atomics::detail::operations<1ul, false>}’  
     while (lock_operations::test_and_set(*static_cast< lock_type* >(m_lock), memory_order_acquire))  
            ^  
libs/atomic/src/lockpool.cpp:111:56: error: ‘lock_type’ does not name a type  
     while (lock_operations::test_and_set(*static_cast< lock_type* >(m_lock), memory_order_acquire))  
                                                        ^  
libs/atomic/src/lockpool.cpp:111:65: error: expected ‘>’ before ‘*’ token  
     while (lock_operations::test_and_set(*static_cast< lock_type* >(m_lock), memory_order_acquire))  
                                                                 ^  
libs/atomic/src/lockpool.cpp:111:65: error: expected ‘(’ before ‘*’ token  
libs/atomic/src/lockpool.cpp:111:67: error: expected primary-expression before ‘>’ token  
     while (lock_operations::test_and_set(*static_cast< lock_type* >(m_lock), memory_order_acquire))  
                                                                   ^  
libs/atomic/src/lockpool.cpp:112:5: error: expected ‘)’ before ‘{’ token  
     {  
     ^  
libs/atomic/src/lockpool.cpp:117:18: error: ‘load’ is not a member of ‘boost::atomics::detail::{anonymous}::lock_operations {aka boost::atomics::detail::operations<1ul, false>}’  
         while (!!lock_operations::load(*static_cast< lock_type* >(m_lock), memory_order_relaxed));  
                  ^  
libs/atomic/src/lockpool.cpp:117:54: error: ‘lock_type’ does not name a type  
         while (!!lock_operations::load(*static_cast< lock_type* >(m_lock), memory_order_relaxed));  
                                                      ^  
libs/atomic/src/lockpool.cpp:117:63: error: expected ‘>’ before ‘*’ token  
         while (!!lock_operations::load(*static_cast< lock_type* >(m_lock), memory_order_relaxed));  
                                                               ^  
libs/atomic/src/lockpool.cpp:117:63: error: expected ‘(’ before ‘*’ token  
libs/atomic/src/lockpool.cpp:117:65: error: expected primary-expression before ‘>’ token  
         while (!!lock_operations::load(*static_cast< lock_type* >(m_lock), memory_order_relaxed));  
                                                                 ^  
libs/atomic/src/lockpool.cpp:117:98: error: expected ‘)’ before ‘;’ token  
         while (!!lock_operations::load(*static_cast< lock_type* >(m_lock), memory_order_relaxed));  
                                                                                                  ^  
libs/atomic/src/lockpool.cpp: In destructor ‘boost::atomics::detail::lockpool::scoped_lock::~scoped_lock()’:  
libs/atomic/src/lockpool.cpp:123:5: error: ‘clear’ is not a member of ‘boost::atomics::detail::{anonymous}::lock_operations {aka boost::atomics::detail::operations<1ul, false>}’  
     lock_operations::clear(*static_cast< lock_type* >(m_lock), memory_order_release);  
     ^  
libs/atomic/src/lockpool.cpp:123:42: error: ‘lock_type’ does not name a type  
     lock_operations::clear(*static_cast< lock_type* >(m_lock), memory_order_release);  
                                          ^  
libs/atomic/src/lockpool.cpp:123:51: error: expected ‘>’ before ‘*’ token  
     lock_operations::clear(*static_cast< lock_type* >(m_lock), memory_order_release);  
                                                   ^  
libs/atomic/src/lockpool.cpp:123:51: error: expected ‘(’ before ‘*’ token  
libs/atomic/src/lockpool.cpp:123:53: error: expected primary-expression before ‘>’ token  
     lock_operations::clear(*static_cast< lock_type* >(m_lock), memory_order_release);  
                                                     ^  
  
    "/usr/bin/aarch64-linux-gnu-g++"   -fPIC -pthread -O3 -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_SOURCE -DNDEBUG  -I"." -c -o "bin.v2/libs/atomic/build/gcc-arm/release/threading-multi/lockpool.o" "libs/atomic/src/lockpool.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/atomic/build/gcc-arm/release/threading-multi/lockpool.o...  
...skipped <pbin.v2/libs/atomic/build/gcc-arm/release/threading-multi>libboost_atomic.so.1.68.0 for lack of <pbin.v2/libs/atomic/build/gcc-arm/release/threading-multi>lockpool.o...  
...skipped <p/opt/cross/boost/lib>libboost_atomic.so.1.68.0 for lack of <pbin.v2/libs/atomic/build/gcc-arm/release/threading-multi>libboost_atomic.so.1.68.0...  
...skipped <p/opt/cross/boost/lib>libboost_atomic.so for lack of <p/opt/cross/boost/lib>libboost_atomic.so.1.68.0...  
  
...  
```  
  
  
Any thoughts on how to get round this?

---

## Comment 1

> Username: EricWu123  
> Created at: 2019-02-25 05:21:12 UTC  
> Url: https://github.com/boostorg/atomic/issues/22#issuecomment-466875690  

i got same problem when i tried to install boost on jetson tx2.   
do you solve this problem?

---

## Comment 2

> Username: Lastique  
> Created at: 2019-02-25 09:30:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/22#issuecomment-466939788  

It looks like your system Boost headers are used instead of the headers from the Boost distribution you're building. If you checked out Boost from git, did you run `b2 headers` before building?  
  
If you did and there are headers in `$BOOST_ROOT/boost` then I'm not sure why that happens. Boost.Build should be providing `$BOOST_ROOT` in the `-I` switches to the compiler (it should look like `-I"."`), you can verify that in the build log if you add `-d+2` argument to `b2` command line. In this case, I suppose, you could work around the problem by uninstalling system Boost headers (`libboost1.67-dev` package in Cosmic).
