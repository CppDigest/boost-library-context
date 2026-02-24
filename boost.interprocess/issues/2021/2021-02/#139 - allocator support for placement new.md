# #139 - allocator support for placement new [Closed]

> Username: liuzqt  
> Created at: 2021-02-10 23:35:42 UTC  
> Updated at: 2021-02-20 22:21:18 UTC  
> Closed at: 2021-02-20 22:21:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/139  

I'm using Boost.Interprocess shared memory, and I'm implementing my own container, expecting to work with both stl allocator and boost ipc allocator.  
When it comes to placement new, the code won't compile.  
  
below is the code to reproduce this  
  
```  
  
#include <memory>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
namespace ipc = boost::interprocess;  
  
typedef ipc::allocator<int, ipc::managed_shared_memory::segment_manager> ShmemAllocator;  
  
int main() {  
    struct shm_remove {  
      shm_remove() { ipc::shared_memory_object::remove("MySharedMemory"); }  
      ~shm_remove() { ipc::shared_memory_object::remove("MySharedMemory"); }  
    } remover;  
  ipc::managed_shared_memory segment(ipc::open_or_create, "MySharedMemory", 1024 * 1024 * 10);  
  
  ShmemAllocator shm_alloc(segment.get_segment_manager());  
  ShmemAllocator::pointer shm_ptr = shm_alloc.allocate(1);  
  
  std::allocator<int> std_alloc;  
  std::allocator<int>::pointer std_ptr = std_alloc.allocate(1);  
    
  new(std_ptr) int(3);  
  // new(shm_ptr) int(3); // this line doesn't work  
  // std::allocator_traits<ShmemAllocator>::construct(shm_alloc, 3); // this line doesn't work  
  new(&*shm_ptr) int(3); // this line somehow work......I don't know why  
  
  return 0;  
}  
```  
  
got the compiler error  
```  
/home/ziqi.liu/ClionProjects/shared_memory_experiment/src/main_a.cpp: In function ‘int main()’:  
/home/ziqi.liu/ClionProjects/shared_memory_experiment/src/main_a.cpp:22:21: error: no matching function for call to ‘operator new(sizetype, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >::pointer&)’  
   new(shm_ptr) int(3); // this line doesn't work  
                     ^  
In file included from /usr/include/c++/5/ext/new_allocator.h:33:0,  
                 from /usr/include/x86_64-linux-gnu/c++/5/bits/c++allocator.h:33,  
                 from /usr/include/c++/5/bits/allocator.h:46,  
                 from /usr/include/c++/5/memory:63,  
                 from /home/ziqi.liu/ClionProjects/shared_memory_experiment/src/main_a.cpp:1:  
/usr/include/c++/5/new:111:7: note: candidate: void* operator new(std::size_t)  
 void* operator new(std::size_t) _GLIBCXX_THROW (std::bad_alloc)  
       ^  
/usr/include/c++/5/new:111:7: note:   candidate expects 1 argument, 2 provided  
/usr/include/c++/5/new:119:7: note: candidate: void* operator new(std::size_t, const std::nothrow_t&)  
 void* operator new(std::size_t, const std::nothrow_t&) _GLIBCXX_USE_NOEXCEPT  
       ^  
/usr/include/c++/5/new:119:7: note:   no known conversion for argument 2 from ‘boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >::pointer {aka boost::interprocess::offset_ptr<int, long int, long unsigned int, 0ul>}’ to ‘const std::nothrow_t&’  
/usr/include/c++/5/new:129:14: note: candidate: void* operator new(std::size_t, void*)  
 inline void* operator new(std::size_t, void* __p) _GLIBCXX_USE_NOEXCEPT  
              ^  
/usr/include/c++/5/new:129:14: note:   no known conversion for argument 2 from ‘boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >::pointer {aka boost::interprocess::offset_ptr<int, long int, long unsigned int, 0ul>}’ to ‘void*’  
In file included from /home/ziqi.liu/.tspkg/include/boost/interprocess/mem_algo/detail/mem_algo_common.hpp:35:0,  
                 from /home/ziqi.liu/.tspkg/include/boost/interprocess/allocators/detail/allocator_common.hpp:35,  
                 from /home/ziqi.liu/.tspkg/include/boost/interprocess/allocators/allocator.hpp:30,  
                 from /home/ziqi.liu/ClionProjects/shared_memory_experiment/src/main_a.cpp:2:  
/home/ziqi.liu/.tspkg/include/boost/container/detail/placement_new.hpp:24:14: note: candidate: void* operator new(std::size_t, void*, boost_container_new_t)  
 inline void *operator new(std::size_t, void *p, boost_container_new_t)  
              ^  
/home/ziqi.liu/.tspkg/include/boost/container/detail/placement_new.hpp:24:14: note:   candidate expects 3 arguments, 2 provided  
CMakeFiles/main_a.dir/build.make:62: recipe for target 'CMakeFiles/main_a.dir/src/main_a.cpp.o' failed  
make[2]: *** [CMakeFiles/main_a.dir/src/main_a.cpp.o] Error 1  
CMakeFiles/Makefile2:162: recipe for target 'CMakeFiles/main_a.dir/all' failed  
make[1]: *** [CMakeFiles/main_a.dir/all] Error 2  
Makefile:83: recipe for target 'all' failed  
make: *** [all] Error 2  
```  
  
  
as shown above, neither the `placement new` or `allocator_traits` can work, but the `&*` magic did the trick(my colleage told me this, I don't know why...)  
  
I'm running boost 1.58, not sure if it's fixed in newer version.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-20 22:21:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/139#issuecomment-782758253  

ShmemAllocator::pointer is not a raw pointer and placement new requires something convertible to void*. This is not a bug, the standard allows non-raw pointers in allocators and does not guarantee "new (shm_ptr) int(3)" will compile.  
  
As you've done, you can use (&*shm_ptr) to make sure a raw pointer is returned (*shm_ptr returns a reference so &*shm_ptr is a raw pointer, convertible to void*).
