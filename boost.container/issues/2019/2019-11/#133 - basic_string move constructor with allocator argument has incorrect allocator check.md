# #133 - basic_string move constructor with allocator argument has incorrect allocator check [Closed]

> Username: ergpudb  
> Created at: 2019-11-05 17:31:46 UTC  
> Updated at: 2019-11-14 14:25:54 UTC  
> Closed at: 2019-11-14 14:25:30 UTC  
> Url: https://github.com/boostorg/container/issues/133  

In the basic_string move constructor with allocator argument:  
  
`basic_string(BOOST_RV_REF(basic_string) s, const allocator_type &a)`  
  
 it does this check (line 715):  
  
`if(a == this->alloc()){`  
  
Shouldn't this be:  
  
`if(s.alloc() == this->alloc()){`  
  
This check should be comparing the allocator of the string being moved from (s) with this's allocator. What it is actually doing is comparing the allocator passed in (which is used to initialize this's allocator) with this's allocator. It isn't considering the allocator of the string being moved from at all. As a result swap_data can end up being called with unequal allocators (which is invalid).  
  
The basic_string move constructor _without_ allocator argument appears to do the correct check (line 685).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-11-14 14:25:54 UTC  
> Url: https://github.com/boostorg/container/issues/133#issuecomment-553910720  

Many thanks for the report, you are right.
