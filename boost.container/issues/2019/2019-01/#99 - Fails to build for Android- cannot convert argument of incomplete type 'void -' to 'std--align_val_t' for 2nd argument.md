# #99 - Fails to build for Android: cannot convert argument of incomplete type 'void *' to 'std::align_val_t' for 2nd argument [Closed]

> Username: berolinux  
> Created at: 2019-01-23 16:12:33 UTC  
> Updated at: 2019-04-30 21:15:13 UTC  
> Closed at: 2019-04-30 21:15:12 UTC  
> Url: https://github.com/boostorg/container/issues/99  

Trying to build boost-container 1.69.0 for Android with the current AOSP master toolchain fails:  
  
```  
In file included from external/boost-container/src/pool_resource.cpp:18:  
external/boost-container/include/boost/container/detail/block_slist.hpp:97:34: error: no matching function for call to 'operator new'  
      block_slist_header &mb  = *::new((void*)p) DerivedFromBlockSlistHeader;  
                                 ^~  
external/boost-container/include/boost/container/detail/block_slist.hpp:142:40: note: in instantiation of member function 'boost::container::pmr::block_slist_base<boost::container::pmr::block_slist_header>::allocate' requested here  
   {  return this->block_slist_base<>::allocate(size, m_upstream_rsrc);  }  
                                       ^  
note: candidate function not viable: cannot convert argument of incomplete type 'void *' to 'std::align_val_t' for 2nd argument  
note: candidate function not viable: requires 1 argument, but 2 were provided  
external/boost-container/include/boost/container/detail/placement_new.hpp:24:14: note: candidate function not viable: requires 3 arguments, but 2 were provided  
inline void *operator new(std::size_t, void *p, boost_container_new_t)  
             ^  
In file included from external/boost-container/src/pool_resource.cpp:17:  
In file included from external/boost-container/include/boost/container/detail/pool_resource.hpp:21:  
external/boost-container/include/boost/container/detail/block_list.hpp:103:33: error: no matching function for call to 'operator new'  
      block_list_header &mb  = *::new((void*)p) DerivedFromBlockListHeader;  
                                ^~  
external/boost-container/src/pool_resource.cpp:213:31: note: in instantiation of member function 'boost::container::pmr::block_list_base<boost::container::pmr::block_list_header>::allocate' requested here  
      return m_oversized_list.allocate(bytes, m_upstream);  
                              ^  
note: candidate function not viable: cannot convert argument of incomplete type 'void *' to 'std::align_val_t' for 2nd argument  
note: candidate function not viable: requires 1 argument, but 2 were provided  
external/boost-container/include/boost/container/detail/placement_new.hpp:24:14: note: candidate function not viable: requires 3 arguments, but 2 were provided  
inline void *operator new(std::size_t, void *p, boost_container_new_t)  
             ^  
2 errors generated.  
17:07:56 ninja failed with: exit status 1  
  
```  
  
Not sure why it would think of void* as an incomplete type... Haven't looked into it a lot, but so far it looks like it may be a toolchain bug.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-01-30 23:28:22 UTC  
> Url: https://github.com/boostorg/container/issues/99#issuecomment-459152444  

Sorry, I can't figure out what can be wrong, but maybe this commit might help:  
  
https://github.com/boostorg/container/commit/b5c25ea058ce3493f12dbc2348c7075cad941976

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-04-24 21:13:38 UTC  
> Url: https://github.com/boostorg/container/issues/99#issuecomment-486428841  

Any news on this issue? Did the commit help?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2019-04-30 21:15:12 UTC  
> Url: https://github.com/boostorg/container/issues/99#issuecomment-488118393  

Closing the issue due to no reply, I'm asumming the commit fixed the situation. Thanks for the report.
