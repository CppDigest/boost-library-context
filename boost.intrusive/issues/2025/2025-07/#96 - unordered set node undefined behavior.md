# #96 - unordered set node undefined behavior [Open]

> Username: kelbon  
> Created at: 2025-07-24 07:07:45 UTC  
> Updated at: 2025-07-24 07:07:45 UTC  
> Url: https://github.com/boostorg/intrusive/issues/96  

Im trying to use vector of buckets for dynamic extension.  
  
My code:  
  
```cpp  
      buckets.resize(buckets.size() * 2);  
      myset.rehash({buckets.data(), buckets.size()});  
```  
  
Code compiles, but it is undefined behavior and ... Thats why:  
  
detail/hashtable_node.hpp:  
  
```cpp  
   inline bucket_impl()  
   {}  
  
   inline bucket_impl(const bucket_impl &)  
   {}  
  
   inline ~bucket_impl()  
   {}  
```  
  
Copy constructor(its move ctor too) does nothing and its even not noexcept, so vector .resize creates invalid nodes, then .rehash works with invalid memory = segfault  
  
..Why? And how to do it correctly?
