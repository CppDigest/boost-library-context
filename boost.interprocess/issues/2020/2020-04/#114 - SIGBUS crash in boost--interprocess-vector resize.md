# #114 - SIGBUS crash in boost::interprocess:vector resize [Closed]

> Username: CouchTomatoSoup  
> Created at: 2020-04-22 22:59:31 UTC  
> Updated at: 2021-04-21 09:57:28 UTC  
> Closed at: 2021-04-21 09:57:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/114  

The program below is repeatedly creating 0.1 MB vectors in shared memory. The shared memory segment grows by 1 MB each time there isn't enough space left to create or resize a vector. Eventually the program crashes on the `vec->resize` call (marked in the code) with a SIGBUS error. The stack trace is below. The crash usually happens when the shared memory segment is around 8G. I have 16G of RAM and only about 1G of it is being used by other processes besides this one.  
  
Am I growing the memory or using the boost::interproces::vector incorrectly here or could there be some problem in the boost library itself?  
  
  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/sync/named_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/container/scoped_allocator.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
#include <iostream>  
#include <unistd.h>  
  
namespace bip = boost::interprocess;    
template <typename T>  
using Alloc   = bip::allocator<T, bip::managed_shared_memory::segment_manager>;  
typedef bip::vector<uint8_t, boost::container::scoped_allocator_adaptor<Alloc<uint8_t> > > DataVector;  
  
  
int main(int argc, char** argv){  
  std::string shm_name = "test";  
  int size_increment = 1000000; // 1 MB  
  int vec_size = 100000; // 0.1 MB  
  assert(vec_size < size_increment);  
  
  bip::shared_memory_object::remove(shm_name.c_str());  
    
  bip::managed_shared_memory* segment = new bip::managed_shared_memory(bip::open_or_create, shm_name.c_str(), size_increment);  
  
  for(int i = 0; true; i++){  
    std::cout << i << std::endl;  
    std::string var_name = std::to_string(i);  
    DataVector* vec = NULL;  
  
    // create the vector  
    try{  
      vec = segment->find_or_construct<DataVector>(var_name.c_str())(segment->get_segment_manager());  
    }  
    catch(std::exception& ex){  
      // grow  
      std::cout << "1 growing: " << ex.what() << std::endl;  
      delete segment;  
      bip::managed_shared_memory::grow(shm_name.c_str(), size_increment);  
      segment = new bip::managed_shared_memory(bip::open_only, shm_name.c_str());  
      std::cout << "1 done growing" << std::endl;  
  
      vec = segment->find_or_construct<DataVector>(var_name.c_str())(segment->get_segment_manager());  
    }  
  
    // make sure the vector isn't null  
    if(vec == NULL){  
      std::cout << "1 vec was NULL" << std::endl;  
      return 1;  
    }  
      
    // grow the vector  
    try{  
      vec->resize(vec_size); // SIGBUS crash happens here  
    }  
    catch(std::exception& ex){  
      // grow  
      std::cout << "2 growing: " << ex.what() << std::endl;  
      delete segment;  
      bip::managed_shared_memory::grow(shm_name.c_str(), size_increment);  
      segment = new bip::managed_shared_memory(bip::open_only, shm_name.c_str());  
      std::cout << "2 done growing" << std::endl;  
  
      vec = segment->find<DataVector>(var_name.c_str()).first;  
      if(vec == NULL){  
	std::cout << "2 vec was NULL" << std::endl;  
	return 1;  
      }  
      vec->resize(vec_size);  
    }  
  
    usleep(1000); // sleep 1ms  
  }  
    
    
  return 0;  
}  
```  
  
```  
Program terminated with signal SIGBUS, Bus error.  
#0  __memset_avx2_erms () at ../sysdeps/x86_64/multiarch/memset-vec-unaligned-erms.S:141  
141	../sysdeps/x86_64/multiarch/memset-vec-unaligned-erms.S: No such file or directory.  
  
#0  __memset_avx2_erms () at ../sysdeps/x86_64/multiarch/memset-vec-unaligned-erms.S:141  
#1  0x0000562cec17143c in boost::container::uninitialized_value_init_alloc_n<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>(boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>&, boost::container::allocator_traits<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::size_type, unsigned char*) (n=100000, r=0x7fd0d0c1ce90 <error: Cannot access memory at address 0x7fd0d0c1ce90>)  
    at /usr/include/boost/container/detail/copy_move_algo.hpp:588  
#2  0x0000562cec16e9b5 in boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>::uninitialized_copy_n_and_update(boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>&, unsigned char*, unsigned long) const (this=0x7ffc3a961780, a=..., p=0x7fd0d0c1ce90 <error: Cannot access memory at address 0x7fd0d0c1ce90>,   
    n=100000) at /usr/include/boost/container/detail/advanced_insert_int.hpp:126  
#3  0x0000562cec16e6a3 in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::priv_forward_range_insert_new_allocation<boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*> >(unsigned char*, unsigned long, unsigned char*, unsigned long, boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>) (this=0x7fd0d0c1ce58, new_start=0x7fd0d0c1ce90 <error: Cannot access memory at address 0x7fd0d0c1ce90>, new_cap=100000, pos=0x0, n=100000, insert_range_proxy=...) at /usr/include/boost/container/vector.hpp:2943  
#4  0x0000562cec16c0ad in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::priv_forward_range_insert_no_capacity<boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*> >(boost::interprocess::offset_ptr<unsigned char, long, unsigned long, 0ul> const&, unsigned long, boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>, boost::move_detail::integral_constant<unsigned int, 1u>) (this=0x7fd0d0c1ce58, pos=..., n=100000, insert_range_proxy=...) at /usr/include/boost/container/vector.hpp:2682  
#5  0x0000562cec16a6f8 in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::priv_forward_range_insert<boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*> >(boost::interprocess::offset_ptr<unsigned char, long, unsigned long, 0ul> const&, unsigned long, boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>) (this=0x7fd0d0c1ce58, pos=..., n=100000, insert_range_proxy=...) at /usr/include/boost/container/vector.hpp:2744  
#6  0x0000562cec168ff4 in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::priv_forward_range_insert_at_end<boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>, boost::move_detail::integral_constant<unsigned int, 1u> >(unsigned long, boost::container::container_detail::insert_value_initialized_n_proxy<boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >>, unsigned char*>, boost::move_detail::integral_constant<unsigned int, 1u>) (this=0x7fd0d0c1ce58, n=100000, insert_range_proxy=...) at /usr/include/boost/container/vector.hpp:2774  
#7  0x0000562cec167e43 in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::priv_resize<boost::container::value_init_t>(unsigned long, boost::container::value_init_t const&) (this=0x7fd0d0c1ce58, new_size=100000, u=...) at /usr/include/boost/container/vector.hpp:2602  
#8  0x0000562cec167838 in boost::container::vector<unsigned char, boost::container::scoped_allocator_adaptor<boost::interprocess::allocator<unsigned char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> >> >::resize(unsigned long) (this=0x7fd0d0c1ce58, new_size=100000) at /usr/include/boost/container/vector.hpp:1460  
#9  0x0000562cec164e4f in main (argc=2, argv=0x7ffc3a9620d8) at /home/username/subt/map_database_ws/src/map_database_demo/src/boost_test.cpp:53  
```

---

## Comment 1

> Username: CouchTomatoSoup  
> Created at: 2020-04-23 16:12:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/114#issuecomment-618491807  

This is happening because /dev/shm is full. It seems that boost allows growing the shared memory file past the limit, but generates a SIGBUS when actually attempting to use the memory. Is this correct behavior? Shouldn't grow() throw an exception if it would be growing past the limit?

---

## Comment 2

> Username: roehling  
> Created at: 2020-12-07 19:05:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/114#issuecomment-740117893  

The problem is known for at least [10 years](https://svn.boost.org/trac10/ticket/4374). I believe the linked bug was wrongly closed as invalid, because `EFBIG` merely means the file is larger than the filesystem supports. Out-of-space conditions would be reported as `ENOSPC`, which is notably absent from the list of possible error codes that `ftruncate()` returns.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2021-04-21 09:57:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/114#issuecomment-823937812  

Closing this issue as #106 was closed, which should avoid SIGBUS issues with shared memory.
