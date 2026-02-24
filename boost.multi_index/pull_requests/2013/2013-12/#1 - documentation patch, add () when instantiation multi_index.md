# #1 documentation patch, add () when instantiation multi_index [Closed]

> Username: alexanderkjall  
> Created at: 2013-12-01 21:14:48 UTC  
> Updated at: 2013-12-01 21:18:05 UTC  
> Closed at: 2013-12-01 21:18:05 UTC  
> Url: https://github.com/boostorg/multi_index/pull/1  

Here is an abridged error message that i get if i don't have () in my code.  
  
error: no matching function for call to 'boost::multi_index::multi_index_container  
[...]  
note: boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::multi_index_container(const boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>&)  
[...]  
note:   candidate expects 1 argument, 0 provided

---
