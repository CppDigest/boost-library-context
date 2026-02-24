# #239 Add unordered container `pmr` aliases [Merged]

> Username: k3DW  
> Created at: 2024-03-23 05:48:53 UTC  
> Updated at: 2024-05-04 17:47:05 UTC  
> Merged at: 2024-03-25 08:58:29 UTC  
> Closed at: 2024-03-25 08:58:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/239  

Closes #229

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-23 05:58:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#issuecomment-2016366990  

An automated preview of the documentation is available at [https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-03-23 19:13:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#issuecomment-2016579324  

An automated preview of the documentation is available at [https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 3 [Commented]

> Username: joaquintides  
> Created_at: 2024-03-24 12:12:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/239#pullrequestreview-1956530180  

📁 doc/unordered/changes.adoc

```diff
  10 |+ 
  11 |+ * Added container `pmr` aliases when header `<memory_resource>` is available. The alias `boost::unordered::pmr::[container]` refers to `boost::unordered::[container]` with a `std::polymorphic_allocator` allocator type.
  12 |+ 
```

> Username: joaquintides  
> Created_at: 2024-03-24 11:34:35 UTC  
> Updated_at: 2024-03-24 12:12:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#discussion_r1536794230  

s/`std::polymorphic_allocator`/`std::pmr::polymorphic_allocator`


📁 test/cfoa/pmr_allocator_tests.cpp

```diff
  58 |+     return std::is_same<typename X::key_type, std::pmr::string>::value
  59 |+              ? key.size() + value.size()
  60 |+              : 0;
```

> Username: joaquintides  
> Created_at: 2024-03-24 11:50:13 UTC  
> Updated_at: 2024-03-24 15:42:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#discussion_r1536796706  

This is grade A+ nitpicking, but the returned value is less than the number of allocated chars because of null terminators.

---

> Username: joaquintides  
> Created_at: 2024-03-24 12:09:39 UTC  
> Updated_at: 2024-03-24 12:12:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#discussion_r1536799958  

This is a near-perfect duplicate of `test/unordered/pmr_allocator_tests.cpp`. When cfoa was introduced, its tests were kept separate because the interface of concurrent containers is too dissimilar to that of regular containers, but in this case the only relevant difference in the testing code is [this portion](https://github.com/k3DW/unordered/blob/feature/229/test/cfoa/pmr_allocator_tests.cpp#L75-L91). Consider unifying the same way as it's done in [explicit_alloc_ctor_tests.cpp](https://github.com/k3DW/unordered/blob/feature/229/test/cfoa/explicit_alloc_ctor_tests.cpp)


📁 test/unordered/pmr_allocator_tests.cpp

```diff
  75 |+     return std::is_same<typename X::key_type, std::pmr::string>::value
  76 |+              ? sv.size()
  77 |+              : 0;
```

> Username: joaquintides  
> Created_at: 2024-03-24 12:10:20 UTC  
> Updated_at: 2024-03-24 12:12:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#discussion_r1536800066  

Same as in `test/cfoa/pmr_allocator_tests.cpp`.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-03-24 17:33:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/239#issuecomment-2016880525  

An automated preview of the documentation is available at [https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://239.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
