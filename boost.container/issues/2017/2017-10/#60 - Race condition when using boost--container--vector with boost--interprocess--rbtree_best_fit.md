# #60 - Race condition when using boost::container::vector with boost::interprocess::rbtree_best_fit [Closed]

> Username: ostash  
> Created at: 2017-10-24 14:47:45 UTC  
> Updated at: 2017-10-26 10:23:19 UTC  
> Closed at: 2017-10-26 10:22:46 UTC  
> Url: https://github.com/boostorg/container/issues/60  

Following minimal test case:  
```c++  
#include <iostream>  
#include <thread>  
#include <vector>  
  
#include <boost/container/vector.hpp>  
#include <boost/interprocess/managed_mapped_file.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
  
using MappedFile = boost::interprocess::basic_managed_mapped_file<char,  
                   boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>,  
                   boost::interprocess::iset_index>;  
  
template <typename T>  
using Allocator = boost::interprocess::allocator<T, MappedFile::segment_manager>;  
  
using VectorInt = boost::container::vector<int, Allocator<int>>;  
  
void fillVector(Allocator<void> alloc)  
{  
  try {  
    Allocator<VectorInt> vAlloc(alloc);  
    auto ptr = vAlloc.allocate(1);  
    vAlloc.construct(ptr, alloc);  
  
    while (true)  
    {  
      ptr->push_back(42);  
    }  
  }  
  catch (const std::exception& ex)  
  {  
    std::cerr << ex.what() << '\n';  
  }  
}  
  
int main()  
{  
  MappedFile file(boost::interprocess::create_only, "MyBlock", 2*1024*1024ul, nullptr, {});  
  Allocator<void> alloc(file.get_segment_manager());  
  
  std::thread th1([alloc]{ fillVector(alloc);});  
  std::thread th2([alloc]{ fillVector(alloc);});  
  
  th1.join();  
  th2.join();  
  
  return 0;  
}  
```  
is considered as racy by ThreadSanitizer:  
```  
WARNING: ThreadSanitizer: data race (pid=63884)  
  Write of size 4 at 0x7f5553a00d10 by thread T1:  
    #0 void boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >::construct<int>(boost::interprocess::offset_ptr<int, long, unsigned long, 0ul> const&, int&&) boost/interprocess/allocators/allocator.hpp:264 (ip_tsan+0x0000004115c5)  
    #1 void boost::container::allocator_traits<boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::priv_construct<int, int>(boost::move_detail::integral_constant<bool, true>, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >&, int*, int&&) boost/container/allocator_traits.hpp:411 (ip_tsan+0x000000408cec)  
    #2 void boost::container::allocator_traits<boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::construct<int, int>(boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >&, int*, int&&) boost/container/allocator_traits.hpp:360 (ip_tsan+0x000000408cec)  
    #3 void boost::container::vector<int, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::priv_push_back<int>(int&&) boost/container/vector.hpp:2573 (ip_tsan+0x000000408cec)  
    #4 boost::container::vector<int, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::push_back(int&&) boost/container/vector.hpp:1857 (ip_tsan+0x00000040340b)  
    #5 fillVector(boost::interprocess::allocator<void, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >) ip_tsan.cpp:27 (ip_tsan+0x00000040340b)  
    #6 operator() ip_tsan.cpp:41 (ip_tsan+0x0000004034d7)  
    #7 __invoke_impl<void, main()::<lambda()> > c++/7.2.0/bits/invoke.h:60 (ip_tsan+0x0000004039c5)  
    #8 __invoke<main()::<lambda()> > c++/7.2.0/bits/invoke.h:95 (ip_tsan+0x0000004036de)  
    #9 _M_invoke<0> c++/7.2.0/thread:234 (ip_tsan+0x0000004046c0)  
    #10 operator() c++/7.2.0/thread:243 (ip_tsan+0x00000040460d)  
    #11 _M_run c++/7.2.0/thread:186 (ip_tsan+0x000000404572)  
    #12 <null> <null> (libstdc++.so.6+0x0000000df8ed)  
  
  Previous write of size 8 at 0x7f5553a00d10 by thread T2 (mutexes: write M9):  
    #0 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::priv_check_and_allocate(unsigned long, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::block_ctrl*, unsigned long&) boost/interprocess/mem_algo/rbtree_best_fit.hpp:1257 (ip_tsan+0x000000410945)  
    #1 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::priv_allocate(int, unsigned long, unsigned long&, void*&, unsigned long) boost/interprocess/mem_algo/rbtree_best_fit.hpp:977 (ip_tsan+0x00000040c33c)  
    #2 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::allocate(unsigned long) boost/interprocess/mem_algo/rbtree_best_fit.hpp:673 (ip_tsan+0x000000409b0d)  
    #3 boost::interprocess::segment_manager_base<boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul> >::allocate(unsigned long) boost/interprocess/segment_manager.hpp:177 (ip_tsan+0x000000408743)  
    #4 boost::interprocess::allocator<boost::container::vector<int, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >::allocate(unsigned long, boost::interprocess::offset_ptr<void const, long, unsigned long, 0ul>) boost/interprocess/allocators/allocator.hpp:153 (ip_tsan+0x000000407982)  
    #5 fillVector(boost::interprocess::allocator<void, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >) ip_tsan.cpp:22 (ip_tsan+0x0000004032c5)  
    #6 operator() ip_tsan.cpp:42 (ip_tsan+0x000000403531)  
    #7 __invoke_impl<void, main()::<lambda()> > c++/7.2.0/bits/invoke.h:60 (ip_tsan+0x000000403b75)  
    #8 __invoke<main()::<lambda()> > c++/7.2.0/bits/invoke.h:95 (ip_tsan+0x0000004037fa)  
    #9 _M_invoke<0> c++/7.2.0/thread:234 (ip_tsan+0x000000404668)  
    #10 operator() c++/7.2.0/thread:243 (ip_tsan+0x0000004045bd)  
    #11 _M_run c++/7.2.0/thread:186 (ip_tsan+0x000000404528)  
    #12 <null> <null> (libstdc++.so.6+0x0000000df8ed)  
  
  Mutex M9 (0x7f5553a00010) created at:  
    #0 pthread_mutex_init <null> (libtsan.so.0+0x0000000291ae)  
    #1 boost::interprocess::ipcdetail::mutex_initializer::mutex_initializer(pthread_mutex_t&, pthread_mutexattr_t&) boost/interprocess/sync/posix/pthread_helpers.hpp:85 (ip_tsan+0x000000406b91)  
    #2 boost::interprocess::ipcdetail::posix_mutex::posix_mutex() boost/interprocess/sync/posix/mutex.hpp:84 (ip_tsan+0x000000406cd1)  
    #3 boost::interprocess::interprocess_mutex::interprocess_mutex() boost/interprocess/sync/interprocess_mutex.hpp:153 (ip_tsan+0x000000406e7a)  
    #4 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::header_t::header_t() boost/interprocess/mem_algo/rbtree_best_fit.hpp:150 (ip_tsan+0x00000041f2b4)  
    #5 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>::rbtree_best_fit(unsigned long, unsigned long) boost/interprocess/mem_algo/rbtree_best_fit.hpp:445 (ip_tsan+0x00000041f324)  
    #6 boost::interprocess::segment_manager_base<boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul> >::segment_manager_base(unsigned long, unsigned long) boost/interprocess/segment_manager.hpp:105 (ip_tsan+0x000000418dbf)  
    #7 boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index>::segment_manager(unsigned long) boost/interprocess/segment_manager.hpp:419 (ip_tsan+0x000000415e0b)  
    #8 boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul>::create_impl(void*, unsigned long) boost/interprocess/detail/managed_memory_impl.hpp:180 (ip_tsan+0x000000412038)  
    #9 boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> >::operator()(void*, unsigned long, bool) const boost/interprocess/detail/managed_memory_impl.hpp:743 (ip_tsan+0x00000040eee6)  
    #10 void boost::interprocess::ipcdetail::managed_open_or_create_impl<boost::interprocess::ipcdetail::file_wrapper, 16ul, true, false>::priv_open_or_create<boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > >(boost::interprocess::ipcdetail::create_enum_t, char const* const&, unsigned long, boost::interprocess::mode_t, void const*, boost::interprocess::permissions const&, boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> >) boost/interprocess/detail/managed_open_or_create_impl.hpp:413 (ip_tsan+0x00000040b2d5)  
    #11 boost::interprocess::ipcdetail::managed_open_or_create_impl<boost::interprocess::ipcdetail::file_wrapper, 16ul, true, false>::managed_open_or_create_impl<boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > >(boost::interprocess::create_only_t, char const* const&, unsigned long, boost::interprocess::mode_t, void const*, boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > const&, boost::interprocess::permissions const&) boost/interprocess/detail/managed_open_or_create_impl.hpp:185 (ip_tsan+0x000000408eff)  
    #12 boost::interprocess::basic_managed_mapped_file<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index>::basic_managed_mapped_file(boost::interprocess::create_only_t, char const*, unsigned long, void const*, boost::interprocess::permissions const&) boost/interprocess/managed_mapped_file.hpp:101 (ip_tsan+0x000000407be2)  
    #13 main ip_tsan.cpp:38 (ip_tsan+0x00000040359a)  
  
  Thread T1 (tid=63886, running) created by main thread at:  
    #0 pthread_create <null> (libtsan.so.0+0x0000000289a0)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0x0000000dfc24)  
    #2 main ip_tsan.cpp:41 (ip_tsan+0x0000004035e2)  
  
  Thread T2 (tid=63887, running) created by main thread at:  
    #0 pthread_create <null> (libtsan.so.0+0x0000000289a0)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0x0000000dfc24)  
    #2 main ip_tsan.cpp:42 (ip_tsan+0x000000403608)  
  
SUMMARY: ThreadSanitizer: data race boost/interprocess/allocators/allocator.hpp:264 in void boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >::construct<int>(boost::interprocess::offset_ptr<int, long, unsigned long, 0ul> const&, int&&)  
```

---

## Comment 1

> Username: ostash  
> Created at: 2017-10-24 16:50:53 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339057170  

Boost 1.65, GCC 7.2.0

---

## Comment 2

> Username: ostash  
> Created at: 2017-10-24 20:56:28 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339128499  

Clang 5.0 reports same

---

## Comment 3

> Username: ostash  
> Created at: 2017-10-24 20:56:52 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339128623  

This is under Linux x86_64

---

## Comment 4

> Username: ostash  
> Created at: 2017-10-25 13:35:17 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339331660  

Managed to minimize it a bit more:  
  
```c++  
#include <boost/interprocess/segment_manager.hpp>  
#include <boost/interprocess/mem_algo/rbtree_best_fit.hpp>  
#include <boost/interprocess/indexes/null_index.hpp>  
#include <boost/interprocess/sync/mutex_family.hpp>  
  
#include <boost/container/vector.hpp>  
  
#include <iostream>  
#include <thread>  
  
namespace bi = boost::interprocess;  
  
using MemoryAlgorithm = bi::rbtree_best_fit<bi::mutex_family, void*, 0ul>;  
using SegmentManager = bi::segment_manager<char, MemoryAlgorithm, bi::null_index>;  
  
template <typename T>  
using Allocator = typename SegmentManager::allocator<T>::type;  
  
using VectorInt = boost::container::vector<unsigned, Allocator<unsigned>>;  
  
void fillVector(SegmentManager* sm)  
{  
  try {  
    void* ptr = sm->allocate(sizeof(VectorInt));  
    VectorInt* vecPtr = new (ptr) VectorInt(sm);  
    std::cerr << pthread_self() << " vector at " << vecPtr << '\n';  
    auto oldData = vecPtr->data();  
    auto oldCap = vecPtr->capacity();  
    while (true)  
    {  
      vecPtr->push_back(0xffffffff);  
      auto data = vecPtr->data();  
      auto cap = vecPtr->capacity();  
      if (data != oldData || cap != oldCap)  
      {  
        std::cerr << pthread_self() << " vect data " << data << ", till " << data + vecPtr->capacity() -1 << '\n';  
        oldData = data;  
        oldCap = cap;  
      }  
    }  
  }  
  catch (const std::exception& ex)  
  {  
    std::cerr << pthread_self() << ": " << ex.what() << '\n';  
  }  
}  
  
int main()  
{  
  constexpr size_t blockSize = 2*1024*1024ul;  
  void* mem = malloc(blockSize);  
  SegmentManager* sm = new (mem) SegmentManager(blockSize);  
  
  std::thread th1([sm]{ fillVector(sm);});  
  std::thread th2([sm]{ fillVector(sm);});  
  
  th1.join();  
  th2.join();  
  
  return 0;  
}   
```  
which gives following output from ThreadSanitizer:  
```  
140042383124224 vector at 0x7f5e288000a0  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e288000f4  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e28800124  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e28800184  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e28800244  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e288003c4  
140042383124224 vect data 0x7f5e288000d0, till 0x7f5e288006c4  
140042374731520 vector at 0x7f5e288006d0  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e28800724  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e28800754  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288007b4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e28800874  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288009f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e28800cf4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288012f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e28801ef4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288036f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288066f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e2880c6f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288186f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288306f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288606f4  
140042374731520 vect data 0x7f5e28800700, till 0x7f5e288c06f4  
==================  
WARNING: ThreadSanitizer: data race (pid=19471)  
  Write of size 4 at 0x7f5e288006c0 by thread T1:  
    #0 void boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> >::construct<unsigned int>(unsigned int* const&, unsigned int&&) /usr/include/boost/interprocess/allocators/allocator.hpp:264:7 (ip_tsan+0x4c5a41)  
    #1 void boost::container::allocator_traits<boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> > >::priv_construct<unsigned int, unsigned int>(boost::move_detail::integral_constant<bool, true>, boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> >&, unsigned int*, unsigned int&&) /usr/include/boost/container/allocator_traits.hpp:411:12 (ip_tsan+0x4c596b)  
    #2 void boost::container::allocator_traits<boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> > >::construct<unsigned int, unsigned int>(boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> >&, unsigned int*, unsigned int&&) /usr/include/boost/container/allocator_traits.hpp:360:10 (ip_tsan+0x4c52d3)  
    #3 void boost::container::vector<unsigned int, boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> > >::priv_push_back<unsigned int>(unsigned int&&) /usr/include/boost/container/vector.hpp:2572:10 (ip_tsan+0x4c50e0)  
    #4 boost::container::vector<unsigned int, boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> > >::push_back(unsigned int&&) /usr/include/boost/container/vector.hpp:1857:4 (ip_tsan+0x4b7f0b)  
    #5 fillVector(boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index>*) /home/ostash/tmp/ip/ip_tsan.cpp:31:15 (ip_tsan+0x4b62db)  
    #6 main::$_0::operator()() const /home/ostash/tmp/ip/ip_tsan.cpp:54:25 (ip_tsan+0x4b7098)  
    #7 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/bits/invoke.h:60:14 (ip_tsan+0x4b7030)  
    #8 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/bits/invoke.h:95:14 (ip_tsan+0x4b6f40)  
    #9 _ZNSt6thread8_InvokerISt5tupleIJZ4mainE3$_0EEE9_M_invokeIJLm0EEEEDTclsr3stdE8__invokespcl10_S_declvalIXT_EEEEESt12_Index_tupleIJXspT_EEE /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:234:13 (ip_tsan+0x4b6ee8)  
    #10 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:243:11 (ip_tsan+0x4b6e88)  
    #11 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:186:13 (ip_tsan+0x4b6c6c)  
    #12 <null> <null> (libstdc++.so.6+0xbad2e)  
  
  Previous write of size 8 at 0x7f5e288006c0 by thread T2 (mutexes: write M9):  
    #0 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::priv_check_and_allocate(unsigned long, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::block_ctrl*, unsigned long&) /usr/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:1257:21 (ip_tsan+0x4bad57)  
    #1 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::priv_allocate(int, unsigned long, unsigned long&, void*&, unsigned long) /usr/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:976:38 (ip_tsan+0x4b8927)  
    #2 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::allocate(unsigned long) /usr/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:673:11 (ip_tsan+0x4b82b5)  
    #3 boost::interprocess::segment_manager_base<boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul> >::allocate(unsigned long) /usr/include/boost/interprocess/segment_manager.hpp:177:37 (ip_tsan+0x4b7ca8)  
    #4 fillVector(boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index>*) /home/ostash/tmp/ip/ip_tsan.cpp:24:21 (ip_tsan+0x4b61d2)  
    #5 main::$_1::operator()() const /home/ostash/tmp/ip/ip_tsan.cpp:55:25 (ip_tsan+0x4b7a48)  
    #6 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/bits/invoke.h:60:14 (ip_tsan+0x4b79e0)  
    #7 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/bits/invoke.h:95:14 (ip_tsan+0x4b78f0)  
    #8 _ZNSt6thread8_InvokerISt5tupleIJZ4mainE3$_1EEE9_M_invokeIJLm0EEEEDTclsr3stdE8__invokespcl10_S_declvalIXT_EEEEESt12_Index_tupleIJXspT_EEE /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:234:13 (ip_tsan+0x4b7898)  
    #9 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:243:11 (ip_tsan+0x4b7838)  
    #10 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() /usr/lib/gcc/x86_64-pc-linux-gnu/7.2.0/include/g++-v7/thread:186:13 (ip_tsan+0x4b761c)  
    #11 <null> <null> (libstdc++.so.6+0xbad2e)  
  
  Location is heap block of size 2097152 at 0x7f5e28800000 allocated by main thread:  
    #0 malloc /var/tmp/portage/sys-libs/compiler-rt-sanitizers-5.0.0/work/compiler-rt-5.0.0.src/lib/tsan/rtl/tsan_interceptors.cc:578 (ip_tsan+0x44b52b)  
    #1 main /home/ostash/tmp/ip/ip_tsan.cpp:51:15 (ip_tsan+0x4b65a2)  
  
  Mutex M9 (0x7f5e28800000) created at:  
    #0 pthread_mutex_init /var/tmp/portage/sys-libs/compiler-rt-sanitizers-5.0.0/work/compiler-rt-5.0.0.src/lib/tsan/rtl/tsan_interceptors.cc:1105 (ip_tsan+0x44c70a)  
    #1 boost::interprocess::ipcdetail::mutex_initializer::mutex_initializer(pthread_mutex_t&, pthread_mutexattr_t&) /usr/include/boost/interprocess/sync/posix/pthread_helpers.hpp:85:13 (ip_tsan+0x4c96b8)  
    #2 boost::interprocess::ipcdetail::posix_mutex::posix_mutex() /usr/include/boost/interprocess/sync/posix/mutex.hpp:84:22 (ip_tsan+0x4c9445)  
    #3 boost::interprocess::interprocess_mutex::interprocess_mutex() /usr/include/boost/interprocess/sync/interprocess_mutex.hpp:153:28 (ip_tsan+0x4c9328)  
    #4 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::header_t::header_t() /usr/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:150:11 (ip_tsan+0x4c8c5c)  
    #5 boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>::rbtree_best_fit(unsigned long, unsigned long) /usr/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:172:4 (ip_tsan+0x4c8abe)  
    #6 boost::interprocess::segment_manager_base<boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul> >::segment_manager_base(unsigned long, unsigned long) /usr/include/boost/interprocess/segment_manager.hpp:105:10 (ip_tsan+0x4c88f8)  
    #7 boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index>::segment_manager(unsigned long) /usr/include/boost/interprocess/segment_manager.hpp:418:10 (ip_tsan+0x4b7fc3)  
    #8 main /home/ostash/tmp/ip/ip_tsan.cpp:52:34 (ip_tsan+0x4b65ba)  
  
  Thread T1 (tid=19473, running) created by main thread at:  
    #0 pthread_create /var/tmp/portage/sys-libs/compiler-rt-sanitizers-5.0.0/work/compiler-rt-5.0.0.src/lib/tsan/rtl/tsan_interceptors.cc:889 (ip_tsan+0x44c326)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xbaff4)  
    #2 main /home/ostash/tmp/ip/ip_tsan.cpp:54:15 (ip_tsan+0x4b65dc)  
  
  Thread T2 (tid=19474, running) created by main thread at:  
    #0 pthread_create /var/tmp/portage/sys-libs/compiler-rt-sanitizers-5.0.0/work/compiler-rt-5.0.0.src/lib/tsan/rtl/tsan_interceptors.cc:889 (ip_tsan+0x44c326)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xbaff4)  
    #2 main /home/ostash/tmp/ip/ip_tsan.cpp:55:15 (ip_tsan+0x4b65f6)  
  
SUMMARY: ThreadSanitizer: data race /usr/include/boost/interprocess/allocators/allocator.hpp:264:7 in void boost::interprocess::allocator<unsigned int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::null_index> >::construct<unsigned int>(unsigned int* const&, unsigned int&&)  
==================  
140042383124224 vect data 0x7f5e288c0700, till 0x7f5e288c12f4  
...  
```  
  
It looks like rbtree_best_fit gives a bit of memory from another block during priv_expand OR boost::container::vector thinks that it can use that memory.

---

## Comment 5

> Username: ostash  
> Created at: 2017-10-26 10:16:38 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339620151  

Ok, I think I found it, this is a race on bit-field (https://github.com/google/sanitizers/wiki/ThreadSanitizerPopularDataRaces#race-on-bit-field).  
  
If we take a look on ```boost::interprocess::rbtree_best_fit::SizeHolder```:  
  
```c++  
struct SizeHolder  
{  
   //!This block's memory size (including block_ctrl  
   //!header) in Alignment units  
   size_type m_prev_size :  sizeof(size_type)*CHAR_BIT;  
   size_type m_size      :  sizeof(size_type)*CHAR_BIT - 2;  
   size_type m_prev_allocated :  1;  
   size_type m_allocated :  1;  
};  
```  
non-synchronized access to ```m_prev_size``` and ```m_size``` is a race.  
From what I understand from reading source, it is allowed to write ```UsableByPreviousChunk``` bytes past allocated block, or to ```m_prev_size``` of next block. But during allocation reads and writes to ```m_size``` are performed.

---

## Comment 6

> Username: ostash  
> Created at: 2017-10-26 10:23:19 UTC  
> Url: https://github.com/boostorg/container/issues/60#issuecomment-339621652  

Reopened as https://github.com/boostorg/interprocess/issues/41
