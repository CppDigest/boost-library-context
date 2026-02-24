# #49 Remove useless allocator copy in map [Merged]

> Username: joker-eph  
> Created at: 2017-05-08 18:10:03 UTC  
> Updated at: 2017-06-02 23:15:38 UTC  
> Merged at: 2017-05-16 14:30:51 UTC  
> Closed at: 2017-05-16 14:30:51 UTC  
> Url: https://github.com/boostorg/container/pull/49  

I never contributed to boost, I'm not sure what kind of test I should write for this? For now, I ran successfully b2 in `boost/libs/container/test` with this patch applied.  
  
This patch adds a variant of map constructors to avoid useless extra allocator copy when using initializer list  
  
Many existing constructors have this form:  
  
  map(std::initializer_list<value_type> il, const Compare& comp = Compare(), const allocator_type& a = allocator_type())  
  
The issue is that a temporary allocator_type is constructed, and  
passed to the base class where it is used to copy-constructed the  
rebound allocator.  
This temporary allocator_type here is always destroyed at the end  
of the map() constructor. For stateful allocators this is not  
desirable.  
The solution is to adopt what libc++ is doing and have to constructors:  
  
  map(std::initializer_list<value_type> il, const Compare& comp = Compare(), const allocator_type& a)  
  
and  
  
  map(std::initializer_list<value_type> il, const Compare& comp = Compare())  
  
This way, unless an allocator is provided by the client, no extra temporary  
creation/destruction occurs.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-05-16 14:31:45 UTC  
> Url: https://github.com/boostorg/container/pull/49#issuecomment-301801123  

Thanks for the patch. I applied it and added some additional changes after reviewing all associative (tree-based and flat_tree based) containers.

---
