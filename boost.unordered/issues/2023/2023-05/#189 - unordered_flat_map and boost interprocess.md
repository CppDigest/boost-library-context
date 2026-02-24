# #189 - unordered_flat_map and boost interprocess [Closed]

> Username: diehard2  
> Created at: 2023-05-30 02:02:35 UTC  
> Updated at: 2023-09-15 21:59:02 UTC  
> Closed at: 2023-09-15 21:59:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/189  

I didn't see an issue for this, but I'm unable to successfully swap in unordered_flat_map into my boost interprocess code. I added a snippet below, its failing on emplace if I use the cached_node allocator. That succeeds with the interprocess standard_allocator, but fails on a client read. I included the rudimentary implementation I'm working on. The interprocess documentation says the unordered containers should all work for this, but with unordered_flat_map being so new, I wasn't sure if this was considered.  
  
```    
class smem_test {  
    using String = basic_string<char, std::char_traits<char>, Allocator>;  
    using PairAllocator = typename Allocator::template rebind<std::pair<const String, String>>::other;  
    using ShmUnorderedMap = boost::unordered_map<String, String,   
                                                          boost::hash<String>,   
                                                          std::equal_to<String>, PairAllocator>;             
      
   void create(const std::unordered_map<std::string, std::string>& input){  
        
            segment = new managed_shared_memory(open_or_create, shmem_segment_name.c_str(), bytes );  
            const PairAllocator alloc_inst (segment->get_segment_manager());  
            const Allocator string_alloc_inst(segment->get_segment_manager());  
            mymap = segment->construct<ShmUnorderedMap>("MyMap")(alloc_inst);  
            m_upgradeable_mutex = segment->construct<boost::interprocess::interprocess_upgradable_mutex>("MyMutex")();  
            std::cout << input.size() << std::endl;  
            for(const auto& [key, value] : input){  
                  mymap->emplace(String(key.data(), string_alloc_inst),  
                                 String(value.data(),  string_alloc_inst));  
            }  
            std::cout << "finished loading" << std::endl;  
            is_server = true;  
    }  
     
  ~smem_cache(){  
            if(segment){  
                if(is_server) {  
                    segment->destroy<ShmUnorderedMap>("MyMap");  
                    boost::interprocess::shared_memory_object::remove(shmem_segment_name.c_str());  
                    delete segment;  
                }  
            }  
        }  
 private:  
        bool is_server = false;  
        ShmUnorderedMap* mymap = nullptr;  
        boost::interprocess::interprocess_upgradable_mutex* m_upgradeable_mutex = nullptr;  
        const std::string shmem_segment_name = "my_memory";  
        managed_shared_memory* segment = nullptr;  
}

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-05-31 03:49:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/189#issuecomment-1569453523  

Interesting. We should be working fine with any conforming Allocator implementation. The `unordered_flat_map` has tests for this.  
  
Would you mind describing your issues in a bit more detail? Are the errors at compile-time and if so, can we see the complete error message? Are the errors at runtime?  
  
If you could, it'd help us tremendously if you make a small repro in godbolt.org

---

## Comment 2

> Username: diehard2  
> Created at: 2023-05-31 15:12:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/189#issuecomment-1570427329  

I unfortunately can't create a repo in godbolt because it requires two executables (code below) and godbolt won't let me construct shared memory. I can't construct the iterator from a different process. If I am in the same process it does work. The below works for boost unordered_map and segfaults in unordered_flat_map at the following line  
  
foa.hpp #232  
  
```  inline int match_available()const  
  {  
    return _mm_movemask_epi8(  
      _mm_cmpeq_epi8(m,_mm_setzero_si128()))&0x7FFF;  
  }  
```  
the call stack is   
  
```#0  0x0000555555559c80 in boost::unordered::detail::foa::group15::match_available (this=0x7f1892921a50) at /home/dev/permanent_data/smem_cache/smem_cache/build/vcpkg_installed/x64-linux-dynamic/include/boost/unordered/detail/foa.hpp:232  
#1  0x0000555555559ccc in boost::unordered::detail::foa::group15::match_occupied (this=0x7f1892921a50) at /home/dev/permanent_data/smem_cache/smem_cache/build/vcpkg_installed/x64-linux-dynamic/include/boost/unordered/detail/foa.hpp:237  
#2  0x000055555555b4e3 in boost::unordered::detail::foa::table<boost::unordered::detail::flat_map_types<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >, boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, boost::hash<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, std::equal_to<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, boost::interprocess::allocator<std::pair<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > const, boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::begin (this=0x7fffbc0ac118) at /home/dev/permanent_data/smem_cache/smem_cache/build/vcpkg_installed/x64-linux-dynamic/include/boost/unordered/detail/foa.hpp:1417  
#3  0x000055555555a904 in boost::unordered::unordered_flat_map<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >, boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >, boost::hash<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, std::equal_to<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, boost::interprocess::allocator<std::pair<boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > const, boost::container::basic_string<char, std::char_traits<char>, boost::interprocess::allocator<char, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > > >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index> > >::begin (this=0x7fffbc0ac118) at /home/dev/permanent_data/smem_cache/smem_cache/build/vcpkg_installed/x64-linux-dynamic/include/boost/unordered/unordered_flat_map.hpp:251  
#4  0x0000555555559e2b in main () at /home/dev/permanent_data/smem_cache/smem_cache/client.cpp:88  
```  
  
  
**Reproducer**  
  
  
**Server**  
  
```   
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/allocators/cached_node_allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include "shared.h"  
#include <stdio.h>  
#include <iostream>  
  
typedef boost::interprocess::allocator<char, boost::interprocess::managed_shared_memory::segment_manager> CharAllocator;  
typedef boost::interprocess::basic_string<char, std::char_traits<char>, CharAllocator> MyString;  
typedef std::pair<const MyString, MyString> MyPair;  
typedef boost::interprocess::allocator<MyPair, boost::interprocess::managed_shared_memory::segment_manager> PairAllocator;  
typedef boost::unordered_flat_map<MyString, MyString, boost::hash<MyString>, std::equal_to<MyString>, PairAllocator> MyUnorderedMap;  
  
int main() {  
  struct shm_remove {  
    shm_remove() { boost::interprocess::shared_memory_object::remove("MySharedMemory"); }  
    ~shm_remove(){ boost::interprocess::shared_memory_object::remove("MySharedMemory"); }  
  } remover;  
    
  boost::interprocess::managed_shared_memory segment(boost::interprocess::create_only, "MySharedMemory", 1000000000);  
    
  const CharAllocator char_alloc (segment.get_segment_manager());  
  const PairAllocator pair_alloc (segment.get_segment_manager());  
  
  MyUnorderedMap *myUnorderedMap = segment.construct<MyUnorderedMap>("MyMap")(pair_alloc);  
  auto input = cache();  
  for(const auto& [key, value] : input){  
                  myUnorderedMap->emplace(MyString(key.data(), char_alloc),  
                                 MyString(value.data(),  char_alloc));  
            }  
  int ch;  
  printf("Press any key to continue...\n");  
  ch = getchar(); // Wait for user input  
  return 0;  
}  
```  
  
**Client**  
  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/allocators/cached_node_allocator.hpp>  
#include <boost/interprocess/allocators/private_node_allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <iostream>  
  
typedef boost::interprocess::allocator<char, boost::interprocess::managed_shared_memory::segment_manager> CharAllocator;  
typedef boost::interprocess::basic_string<char, std::char_traits<char>, CharAllocator> MyString;  
typedef std::pair<const MyString, MyString> MyPair;  
typedef boost::interprocess::allocator<MyPair, boost::interprocess::managed_shared_memory::segment_manager> PairAllocator;  
typedef boost::unordered_flat_map<MyString, MyString, boost::hash<MyString>, std::equal_to<MyString>, PairAllocator> MyUnorderedMap;  
  
int main() {  
  // Open the shared memory object  
  boost::interprocess::managed_shared_memory segment(boost::interprocess::open_only, "MySharedMemory");  
  
  // Find the unordered_map in the shared memory segment  
  MyUnorderedMap *myUnorderedMap = segment.find<MyUnorderedMap>("MyMap").first;  
  
  // Check if the unordered_map was found  
  if(myUnorderedMap) {  
    for(auto iter = myUnorderedMap->begin(); iter != myUnorderedMap->end(); ++iter) {  
      std::cout << iter->first << ": " << iter->second << std::endl;  
    }  
  } else {  
    std::cout << "Failed to find the unordered_map." << std::endl;  
  }  
  
  return 0;  
}  
```

---

## Comment 3

> Username: cmazakas  
> Created at: 2023-05-31 16:31:10 UTC  
> Url: https://github.com/boostorg/unordered/issues/189#issuecomment-1570551756  

Alright, I've successfully recreated the issue on my end. You're correct, this works with `boost::unordered_map` but segfaults with `boost::unordered_flat_map`. I'm going to investigate this and I'll report back with any findings.

---

## Comment 4

> Username: cmazakas  
> Created at: 2023-05-31 17:35:06 UTC  
> Updated at: 2023-05-31 17:35:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/189#issuecomment-1570636778  

Alright, we understand the issue now.  
  
Unfortunately, the unordered_flat_map internally stores raw pointers, not fancy pointers like Boost's Interprocess requires.   
  
To this end, the raw pointers stored in one map are not valid to any other process because of how the memory mapping works, i.e. `mmap` returns different addresses for the same shared memory segment.  
  
Unfortunately, refactoring the flat maps to accommodate this isn't straight-forward or trivial. I doubt we'll get this out in time for the next Boost release.  
  
But we're aware of the issue and have it on our backlog. In the interim, stick with `boost::unordered_map` or some other container while we attempt to sort this out.
