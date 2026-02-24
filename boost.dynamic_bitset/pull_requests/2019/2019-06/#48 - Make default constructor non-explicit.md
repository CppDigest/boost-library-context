# #48 Make default constructor non-explicit [Merged]

> Username: kariya-mitsuru  
> Created at: 2019-06-10 07:21:01 UTC  
> Updated at: 2019-06-13 05:26:50 UTC  
> Merged at: 2019-06-12 23:45:12 UTC  
> Closed at: 2019-06-12 23:45:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48  

IMHO, it is better to support copy-initialization with default constructor.  
  
cf. LWG Issue 2193. Default constructors for standard library containers are explicit  
link: https://wg21.cmeerw.net/lwg/issue2193

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2019-06-12 03:46:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#pullrequestreview-248533418  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
 129 | 
 130 |     // constructors, etc.
 131 |+     dynamic_bitset() : m_bits(Allocator()), m_num_bits(0) {}
```

> Username: glenfe  
> Created_at: 2019-06-12 03:39:46 UTC  
> Updated_at: 2019-06-12 23:40:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#discussion_r292736138  

Why `m_bits(Allocator())` instead of `m_bits()`? In fact, this could just be: `dynamic_bitset() : m_num_bits(0) {}`. But no need to invoke `vector(Allocator())` instead of just `vector()` which would already value-initialize the `Allocator`.


📁 dynamic_bitset.html

```diff
 581 |+ <tt>Allocator</tt>.<br />
 582 |  <b>Postconditions:</b> <tt>this-&gt;size() == 0</tt>.<br />
 583 |  <b>Throws:</b> An allocation error if memory is exhausted
```

> Username: glenfe  
> Created_at: 2019-06-12 03:45:35 UTC  
> Updated_at: 2019-06-12 23:40:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#discussion_r292737051  

This `Throws:` isn't right. When default-constructing a `dynamic_bitset` there won't be an "allocation error if memory is exhausted". What could throw is if the `Allocator` constructor throws an exception. i.e. We default construct a `vector<T, Allocator>` and the `vector()` constructor is `noexcept(noexcept(Allocator()))`. i.e. In this constructor we aren't allocating anything.


---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2019-06-12 18:51:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#pullrequestreview-248952902  

Just one documentation change, and then it looks good to me.

📁 dynamic_bitset.html

```diff
 598 |  <b>Postconditions:</b> <tt>this-&gt;size() == 0</tt>.<br />
 599 |  <b>Throws:</b> An allocation error if memory is exhausted
```

> Username: glenfe  
> Created_at: 2019-06-12 18:50:43 UTC  
> Updated_at: 2019-06-12 23:40:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#discussion_r293065404  

This "Throws:" can be removed or be changed to "Throws: Nothing". This is because this constructor should not throw any exceptions. No Allocator is being constructed here, only copied, and copying Allocators is required to not throw exceptions.


---

## Comment 3

> Username: glenfe  
> Created_at: 2019-06-12 23:46:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#issuecomment-501494674  

@kariya-mitsuru Thank you for the contribution.

---

## Comment 4

> Username: kariya-mitsuru  
> Created_at: 2019-06-13 05:26:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/48#issuecomment-501553714  

Thank you for your quick response!

---
