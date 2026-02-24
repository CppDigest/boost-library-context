# #147 - how to grow managed_mapped_file when it used to store map<> [Closed]

> Username: niXman  
> Created at: 2021-07-14 07:10:04 UTC  
> Updated at: 2021-08-10 12:34:08 UTC  
> Closed at: 2021-08-10 12:34:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147  

hi guys!  
  
I want to use boost::interprocess to store some resources (strings) into `bi::map<bi::string, bi::string>` that uses `bi::managed_mapped_file` so I can put the resources in the map once, take the file, and distribute it with the program.  
  
the problem is that I cannot calculate the required size in advance, so I want to use `bi::managed_mapped_file::grow ()` before inserting another element into the map.  
but it doesn't work as i expected, at some point i get exception: `what(): boost::interprocess::bad_alloc`  
  
I know the length of the key string and the length of the value string, how can I find out the size by which I must `grow()` before inserting another element into the map?  
  
now my code looks like this:  
```  
#include <boost/interprocess/managed_mapped_file.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/map.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <iostream>  
int main () {  
     namespace bi = boost::interprocess;  
     using segment_manager_t = bi::managed_mapped_file::segment_manager;  
     using void_allocator = bi::allocator<void, segment_manager_t>;  
     using char_allocator = bi::allocator<char, segment_manager_t>;  
     using char_string = bi::basic_string<char, std::char_traits<char>, char_allocator>;  
     using map_value_type = std::pair<const char_string, char_string>;  
     using map_value_type_allocator = bi::allocator<map_value_type, segment_manager_t>;  
     using map_type = bi::map<char_string, char_string, std::less<char_string>, map_value_type_allocator>;  
  
     static const char *mapfname = "map.dat";  
     bi::managed_mapped_file mmfile(bi::open_or_create, mapfname, 1024);  
     {  
         void_allocator alloc_inst(mmfile.get_segment_manager());  
         map_type *mymap = mmfile.find_or_construct<map_type>("MyMap")(alloc_inst);  
         assert(mymap);  
         for ( int i = 0; i < 100; ++i ) {  
             std::string s = std::to_string(i);  
             char_string key(s.c_str(), alloc_inst);  
             char_string value(s.c_str(), alloc_inst);  
  
              // bi::managed_mapped_file::grow(mapfname, ???); <<<<<<<<<<<<<<<<<<<<<<  
  
             mymap->emplace(std::move(key), std::move(value));  
         }  
     }  
     bi::managed_mapped_file::shrink_to_fit(mapfname);  
  
     return 0;  
}  
  
```  
(or: https://wandbox.org/permlink/jtTOzlMh16ZS8jG6)  
  
any ideas?  
  
best!

---

## Comment 1

> Username: niXman  
> Created at: 2021-07-21 13:23:50 UTC  
> Updated at: 2021-07-21 13:24:21 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147#issuecomment-884187045  

can anyone please ping Ion Gaztañaga the author of boost.interprocess?  
  
thanks!

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-07-21 20:12:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147#issuecomment-884467833  

First of all, you can't grow the mapped file wile it's open. "grow" modifies internal data structures and the previous mapping might be invalid, see:  
 https://www.boost.org/doc/libs/1_76_0/doc/html/interprocess/managed_memory_segments.html#interprocess.managed_memory_segments.managed_memory_segment_advanced_features.growing_managed_memory  
   
 The size to grow can't be know before hand because there is additional metadata to be stored for each allocation. In your case, you need to allocate a map node, and also memory for two strings. "grow" is also a non-trivial operation so calling grow for each insertion is overkill, a bit like reallocating a vector for each insertion. I'd double the size of the mapped file or increase it a 50% for each "grow" call.

---

## Comment 3

> Username: niXman  
> Created at: 2021-07-21 23:55:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147#issuecomment-884570796  

@igaztanaga thank you for answer!  
  
  
that is, do you suggest opening a memory-mapped-file with an initial size, for example 1Mb, adding entries to it until the bad_alloc exception is thrown, close the file and reopen it with a size of 2 Mb?  
right?  
  
let me ask you one more question.  
let's say I know the length of the key string, and the length of the value string. how can I find out how many bytes in the file will actually be used to store these lines?  
  
best!

---

## Comment 4

> Username: niXman  
> Created at: 2021-08-02 09:27:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147#issuecomment-890874596  

@igaztanaga could you answer please?

---

## Comment 5

> Username: igaztanaga  
> Created at: 2021-08-07 22:47:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/147#issuecomment-894714912  

It is not easy to determine how many bytes you will need, just like it is not easy to determine it when allocating something in heap (it depends on the alignment, fragmentation, etc.). In Boost.Interprocess there are bookeeping data structures for each allocation. For your map you need to allocate the tree-node and two additional allocations for he key string and the value string.  In theory, you'll need aprox. 64 bytes of overhead for each allocation, but it's better to test it yourself observing how get_free_memory() evolves.
