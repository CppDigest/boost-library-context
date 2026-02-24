# #247 - destruction of move-constructed map using private_adaptive_pool triggers Assertion `n_free_nodes == m_totally_free_blocks' failed. [Closed]

> Username: aleden  
> Created at: 2025-01-02 08:35:24 UTC  
> Updated at: 2025-11-22 23:44:51 UTC  
> Closed at: 2025-11-22 23:44:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/247  

Minimal reproducible example here: https://gcc.godbolt.org/z/KGefcocv6  
  
```cpp  
#include <boost/interprocess/allocators/private_adaptive_pool.hpp>  
#include <boost/interprocess/containers/map.hpp>  
#include <boost/interprocess/managed_heap_memory.hpp>  
#include <cstdint>  
  
typedef boost::interprocess::managed_heap_memory::segment_manager  
    segment_manager_t;  
  
typedef boost::interprocess::map<  
    uint32_t, uint32_t, std::less<uint32_t>,  
    boost::interprocess::private_adaptive_pool<  
        std::pair<const uint32_t, uint32_t>, segment_manager_t>>  
    mymap_t;  
  
int main() {  
  boost::interprocess::managed_heap_memory heap_mem(1u << 20);  
  
  {  
    mymap_t bbmap(heap_mem.get_segment_manager());  
  
    bbmap.emplace(1, 2);  
  
    mymap_t bbmap2(boost::move(bbmap));  
  } // <= CRASHES HERE  
  
  return 0;  
}  
```  
  
```bash  
$ g++ -o test test.cpp && ./test  
test: /usr/include/boost/container/detail/adaptive_node_pool_impl.hpp:932: void boost::container::dtl::private_adaptive_node_pool_impl_common<SegmentManagerBase, Flags>::priv_clear(size_type, size_type, size_type) [with SegmentManagerBase = boost::interprocess::segment_manager_base<boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family> >; unsigned int Flags = 6; size_type = long unsigned int]: Assertion `n_free_nodes == m_totally_free_blocks' failed.  
Aborted (core dumped)  
```

---

## Comment 1

> Username: aleden  
> Created at: 2025-01-02 20:39:09 UTC  
> Url: https://github.com/boostorg/interprocess/issues/247#issuecomment-2568350045  

This bug is also present with `private_node_allocator`.  
  
```cpp  
#include <boost/interprocess/allocators/private_node_allocator.hpp>                                                                                                                                                                   
#include <boost/interprocess/containers/map.hpp>                                   
#include <boost/interprocess/managed_heap_memory.hpp>                              
#include <cstdint>                                                                 
                                                                                   
typedef boost::interprocess::managed_heap_memory::segment_manager                  
    segment_manager_t;                                                             
                                                                                   
typedef boost::interprocess::map<                                                  
    uint32_t, uint32_t, std::less<uint32_t>,                                       
    boost::interprocess::private_node_allocator<                                   
        std::pair<const uint32_t, uint32_t>, segment_manager_t>>                   
    mymap_t;                                                                       
                                                                                   
int main() {                                                                       
  boost::interprocess::managed_heap_memory heap_mem(1u << 20);                     
                                                                                   
  {                                                                                
    mymap_t bbmap(heap_mem.get_segment_manager());                                 
                                                                                   
    bbmap.emplace(1, 2);                                                           
                                                                                   
    { mymap_t bbmap2(boost::move(bbmap)); }                                        
  }                                                                                
                                                                                   
  return 0;                                                                        
}                                                                                  
```  
  
I'm suspecting that something is wrong with `interprocess::map`, since `interprocess::flat_map` seems to be fine with these examples.

---

## Comment 2

> Username: aleden  
> Created at: 2025-01-02 20:50:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/247#issuecomment-2568361476  

I just confirmed that if the same allocator instance is used, there is no assertion triggered. i.e.  
  
```cpp  
#include <boost/interprocess/allocators/private_adaptive_pool.hpp>                 
#include <boost/interprocess/containers/map.hpp>                                   
#include <boost/interprocess/managed_heap_memory.hpp>                              
#include <cstdint>                                                                 
                                                                                   
typedef boost::interprocess::managed_heap_memory::segment_manager                  
    segment_manager_t;                                                             
                                                                                   
typedef boost::interprocess::map<                                                  
    uint32_t, uint32_t, std::less<uint32_t>,                                       
    boost::interprocess::private_adaptive_pool<                                    
        std::pair<const uint32_t, uint32_t>, segment_manager_t>>                   
    mymap_t;                                                                       
                                                                                   
int main() {                                                                       
  boost::interprocess::managed_heap_memory heap_mem(1u << 20);                     
                                                                                   
  boost::interprocess::private_adaptive_pool<std::pair<const uint32_t, uint32_t>, segment_manager_t> A(heap_mem.get_segment_manager());  
                                                                                   
  {                                                                                
    mymap_t bbmap(A);                                                              
                                                                                   
    bbmap.emplace(1, 2);                                                           
                                                                                                                                                                                                                                      
    { mymap_t bbmap2(A);                                                           
      bbmap2 = boost::move(bbmap); }                                               
  }                                                                                
                                                                                   
  return 0;                                                                        
}                                                                                  
```  
  
Works.  
  
Moving one container into another container that does not share exactly the same allocator instance triggers a deep-copy‐and‐reinsert of all elements. So I'm guessing there's a bug in the deep-copy somewhere.
