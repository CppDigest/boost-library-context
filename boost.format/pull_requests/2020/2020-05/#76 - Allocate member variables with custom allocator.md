# #76 Allocate member variables with custom allocator [Open]

> Username: quantumwizard  
> Created at: 2020-05-29 06:04:11 UTC  
> Updated at: 2020-06-02 04:18:39 UTC  
> Url: https://github.com/boostorg/format/pull/76  

member std::vector variables should use template parameter Allocator instead of default std::allocator

---

## Comment 1

> Username: mclow  
> Created_at: 2020-05-29 14:36:41 UTC  
> Url: https://github.com/boostorg/format/pull/76#issuecomment-636008816  

This shouldn't work; because `std::vector` requires it's allocator's `value_type` to match the `value_type` of the vector.  libc++, for example, will `static_assert` to check this.

---

## Comment 2

> Username: quantumwizard  
> Created_at: 2020-05-29 16:14:17 UTC  
> Url: https://github.com/boostorg/format/pull/76#issuecomment-636058855  

@mclow I made changes to rebind the allocator for the correct types

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-05-29 17:51:36 UTC  
> Updated_at: 2020-06-02 04:18:39 UTC  
> Url: https://github.com/boostorg/format/pull/76#issuecomment-636103279  

# [Codecov](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=h1) Report  
> Merging [#76](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/format/commit/5884c3d8140cdcd2d1bef374fcc216244d5faaa5&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/format/pull/76/graphs/tree.svg?width=650&height=150&src=pr&token=9WwfaSgmDb)](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #76   +/-   ##  
========================================  
  Coverage    64.33%   64.33%             
========================================  
  Files           10       10             
  Lines          816      816             
  Branches       290      290             
========================================  
  Hits           525      525             
  Misses         125      125             
  Partials       166      166             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/format/format\_class.hpp](https://codecov.io/gh/boostorg/format/pull/76/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvZm9ybWF0X2NsYXNzLmhwcA==) | `87.50% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=footer). Last update [5884c3d...a561501](https://codecov.io/gh/boostorg/format/pull/76?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: mclow  
> Created_at: 2020-05-29 18:21:18 UTC  
> Url: https://github.com/boostorg/format/pull/76#issuecomment-636116477  

That's better. I will give this a try (and probably commit it), but this is not even close to right. (the code, not the patch).  
  
There's no provision for actually _supplying_ an allocator to this class. It's all "default construct" everywhere. This works great for stateless allocators, but not so well for stateful ones.

---

## Review 5 [Changes requested]

> Username: glenfe  
> Created_at: 2020-05-31 14:05:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/format/pull/76#pullrequestreview-421498074  

📁 include/boost/format/format_class.hpp

```diff
 165 |-         std::vector<bool> bound_; // stores which arguments were bound. size() == 0 || num_args
 164 |+         std::vector<format_item_t,typename Alloc::template rebind<format_item_t>::other >  items_; // each '%..' directive leads to a format_item
 165 |+         std::vector<bool, typename Alloc::template rebind<bool>::other > bound_; // stores which arguments were bound. size() == 0 || num_args
```

> Username: glenfe  
> Created_at: 2020-05-31 14:04:26 UTC  
> Updated_at: 2020-06-01 20:23:03 UTC  
> Url: https://github.com/boostorg/format/pull/76#discussion_r432950576  

This isn't right, because you can't assume that an Allocator has a member template `rebind`. In fact with `std::allocator` this will not even compile in C++20 where the `rebind` member was removed. Instead of `typename A::template rebind<T>::other` use `typename boost::allocator_rebind<A, T>::type`. Include `<boost/core/allocator_access.hpp>`.


---
