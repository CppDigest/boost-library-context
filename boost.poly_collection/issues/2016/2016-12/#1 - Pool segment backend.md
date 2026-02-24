# #1 - Pool segment backend [Open]

> Username: holocronweaver  
> Created at: 2016-12-23 03:55:28 UTC  
> Updated at: 2016-12-30 22:58:32 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/1  

My understanding is currently segments are stored in std::vectors.   
  
If I wanted to create a base_collection using a pool instead of a vector, would I just have to create a new segment_backend using a pool class for storage?  
  
It seems using a pool backend would avoid the problem of vector memory reallocation causing object pointers and iterators to become invalid.  
  
Motivation: I want to make object creation and deletion cheaper while retaining a polymorphic collection. Truly pie in the sky. =)

---

## Comment 1

> Username: joaquintides  
> Created at: 2016-12-30 16:19:30 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/1#issuecomment-269790160  

Could you please elaborate what you mean by "pool"? One key feature of this data structure is that same-type elements are stored contiguously for maximum processing performance, not sure if this pool thing also provides such a guarantee.

---

## Comment 2

> Username: holocronweaver  
> Created at: 2016-12-30 22:54:48 UTC  
> Updated at: 2016-12-30 22:58:32 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/1#issuecomment-269832044  

The object pool I have in mind works based on blocks of contiguous memory. The default number of objects per block is declared at pool creation. When the pool runs out of memory, it creates a new block (as opposed to reallocating memory like std::vector). An optimization is to adjust block size for the application, ideally resulting in only one block being created by each pool. Pool classes can also be designed to require a single block (effectively acting like an array), but I prefer the flexibility of dynamic storage in practice.  
  
Thus objects are stored semi-contiguously and can be iterated over if the iterator is made aware of the pool block list. The more blocks there are, the less contiguous memory becomes, but with application-specific block size tweaking you can ensure full contiguity just like a std::vector.  
  
An object pool does more than block allocation. It also keeps track of which object indices are free. Object creation requests are fulfilled with the next free index, while deletion is fulfilled by calling the destructor and adding the index to the free list. This is ideal for the case of frequent unordered object deletion and creation, which vectors handle very poorly.  
  
It is possible to make pools conform to most of the std::vector interface, in case that makes a difference. However, most, like boost::object_pool, choose a simpler interface and even forego iterators, though that isn't necessary.
