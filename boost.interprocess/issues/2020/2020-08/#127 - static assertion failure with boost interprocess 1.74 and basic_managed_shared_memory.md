# #127 - static assertion failure with boost interprocess 1.74 and basic_managed_shared_memory [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2020-08-11 11:50:19 UTC  
> Updated at: 2020-08-12 08:19:59 UTC  
> Closed at: 2020-08-11 20:42:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/127  

Hi,  
  
I am trying to move from Boost 1.73 to the rc1 of Boost 1.74. On this very simple snippet, I do have an assertion failure:  
  
```  
#include "boost/interprocess/managed_shared_memory.hpp"  
#include "boost/interprocess/indexes/null_index.hpp"  
#include "boost/interprocess/sync/null_mutex.hpp"  
  
using T = boost::interprocess::basic_managed_shared_memory<char, boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>, boost::interprocess::null_index>;  
  
T shared_memory(boost::interprocess::open_read_only, "/some/path");  
```  
  
I have this error (Linux, x64, and with both gcc 11 (trunk) and clang 12 (trunk):  
  
```  
In file included from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/move/utility_core.hpp:32,  
                 from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/utilities.hpp:29,  
                 from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/managed_memory_impl.hpp:26,  
                 from /data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/managed_shared_memory.hpp:25,  
                 from plop.cpp:1:  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/segment_manager.hpp: In instantiation of ‘boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::s  
egment_manager(boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::size_type) [with CharType = char; MemoryAlgorithm = boost::interprocess::rbtree_best_fit<b  
oost::interprocess::null_mutex_family>; IndexType = boost::interprocess::null_index; boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::size_type = long uns  
igned int]’:  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/managed_memory_impl.hpp:181:28:   required from ‘bool boost::interprocess::ipcdetail::basic_managed_memory_impl<  
CharType, MemoryAlgorithm, IndexType, Offset>::create_impl(void*, boost::interprocess::ipcdetail::basic_managed_memory_impl<CharType, MemoryAlgorithm, IndexType, Offset>::size_type)  
 [with CharType = char; MemoryAlgorithm = boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>; IndexType = boost::interprocess::null_index; long unsigned int Offset = 16; boost::interprocess::ipcdetail::basic_managed_memory_impl<CharType, MemoryAlgorithm, IndexType, Offset>::size_type = long unsigned int]’   
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/managed_memory_impl.hpp:744:40:   required from ‘bool boost::interprocess::ipcdetail::create_open_func<BasicManagedMemoryImpl>::operator()(void*, std::size_t, bool) const [with BasicManagedMemoryImpl = boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>, boost::interprocess::null_index, 16>; std::size_t = long unsigned int]’   
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp:413:33:   required from ‘void boost::interprocess::ipcdetail::managed_open_or_create_impl<DeviceAbstraction, MemAlignment, FileBased, StoreDevice>::priv_open_or_create(boost::interprocess::ipcdetail::create_enum_t, const device_id_t&, std::size_t, boost::interprocess::mode_t, const void*, const boost::interprocess::permissions&, ConstructFunc) [with ConstructFunc = boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>, boost::interprocess::null_index, 16> >; DeviceAbstraction = boost::interprocess::shared_memory_object; long unsigned int MemAlignment = 16; bool FileBased = true; bool StoreDevice = false; boost::interprocess::ipcdetail::managed_open_or_create_impl<DeviceAbstraction, MemAlignment, FileBased, StoreDevice>::device_id_t = const char*; std::size_t = long unsigned int]’   
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp:202:10:   required from ‘boost::interprocess::ipcdetail::managed_open_or_create_impl<DeviceAbstraction, MemAlignment, FileBased, StoreDevice>::managed_open_or_create_impl(boost::interprocess::open_only_t, const device_id_t&, boost::interprocess::mode_t, const void*, const ConstructFunc&) [with ConstructFunc = boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>, boost::interprocess::null_index, 16> >; DeviceAbstraction = boost::interprocess::shared_memory_object; long unsigned int MemAlignment = 16; bool FileBased = true; bool StoreDevice = false; boost::interprocess::ipcdetail::managed_open_or_create_impl<DeviceAbstraction, MemAlignment, FileBased, StoreDevice>::device_id_t = const char*]’   
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/managed_shared_memory.hpp:141:35:   required from ‘boost::interprocess::basic_managed_shared_memory<CharType, MemoryAlgorithm, IndexType>::basic_managed_shared_memory(boost::interprocess::open_read_only_t, const char*, const void*) [with CharType = char; AllocationAlgorithm = boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family>; IndexType = boost::interprocess::null_index]’   
plop.cpp:7:66:   required from here  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/interprocess/segment_manager.hpp:434:78: error: static assertion failed: (boost::move_detail::alignment_of<header_t>::value >= void_ptr_alignment)   
  434 |       BOOST_STATIC_ASSERT((boost::move_detail::alignment_of<header_t>::value >= void_ptr_alignment));  
      |                           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~  
/data/mwrep/res/osp/Boost/21-0-0-0/include/boost/static_assert.hpp:70:55: note: in definition of macro ‘BOOST_STATIC_ASSERT’  
   70 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
```  
  
For me the size of header_t is 2, and the alignment is 1 (and it looks like these are the "normal" values). So isn't this assertion wrong ? Or shall alignment of header_t be overriden to be at least 8 ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-08-11 20:22:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/127#issuecomment-672259653  

Hi Romain,  
  
I think that static assert is wrong, I didn't check with null_index headers. In general, the memory algorithm (which has pointers), has to be aligned to pointers, the header must be just aligned to it's natural alignment. I think most of those static asserts could be removed, as the important part is to have correctly runtime alignment for the segment manager.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created at: 2020-08-11 23:28:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/127#issuecomment-672354600  

Thanks !  
  
I know the release of 1.74 is imminent (according to the original release schedule, it should normally have happened today, but I just saw a rc2 was released instead), but do you think this shall still make it into 1.74 ? Or people shall backport this commit themselves ?  
  
Cheers,  
Romain

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-08-12 08:19:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/127#issuecomment-672721964  

I think it's too late for 1.74, as we need some days of develop cycling, then a merge to master and another cycling...
