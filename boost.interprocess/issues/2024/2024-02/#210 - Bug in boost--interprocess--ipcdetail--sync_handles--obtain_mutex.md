# #210 - Bug in boost::interprocess::ipcdetail::sync_handles::obtain_mutex [Closed]

> Username: skorniakov  
> Created at: 2024-02-09 14:53:34 UTC  
> Updated at: 2024-08-07 23:26:14 UTC  
> Closed at: 2024-08-07 23:26:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/210  

Version 1.84. Insertion at boost\interprocess\sync\windows\sync_utils.hpp line 188  
`  
umap_type::iterator it = umap_.insert(v).first;   
`  
invalidates iterators in map_ if rehashing of umap_ occurred. Later, due to this, program crashed in destroy_syncs_in_range (sync_utils.hpp line 252) at   
`  
umap_.erase(uit);  
`

---

## Comment 1

> Username: Fedr  
> Created at: 2024-07-23 11:41:03 UTC  
> Updated at: 2024-07-23 12:09:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/210#issuecomment-2245006081  

In Boost 1.85, `umap_` is [flat_map](https://www.boost.io/doc/libs/1_85_0/doc/html/boost/container/flat_map.html), so it is never rehashed.  
  
And there is typically no issue with `umap_type::iterator it = umap_.insert(v).first;`, because every new `v` has larger value than existing ones, because of `sync_id` constructor:  
```  
   sync_id()  
   {  winapi::query_performance_counter(&rand_);  }  
```  
  
The actual issue is in `destroy_syncs_in_range`:  
```  
      for (; it != ithig; ++it){  
         id_map_type::iterator uit = it->second;   
         void * const hnd = uit->second;  
         umap_.erase(uit);  
         int ret = winapi::close_handle(hnd);  
         --num_handles_;  
         BOOST_ASSERT(ret != 0); (void)ret;  //Sanity check that handle was ok  
      }  
```  
Here `umap_.erase(uit);` invalidates all iterators with larger keys, so the second call `umap_.erase(uit);` can crash.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-08-07 23:26:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/210#issuecomment-2274511508  

Thanks for the report, fixed with the proposed pull request.
