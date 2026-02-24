# #103 - No implementation for `merge()`ing map and multimap/set and multiset [Closed]

> Username: cmazakas  
> Created at: 2022-02-14 23:20:12 UTC  
> Updated at: 2022-02-26 02:00:57 UTC  
> Closed at: 2022-02-26 02:00:57 UTC  
> Url: https://github.com/boostorg/unordered/issues/103  



---

## Comment 1

> Username: cmazakas  
> Created at: 2022-02-18 18:13:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/103#issuecomment-1044951809  

The documentation in the reference is incorrect. These methods actually are implemented, the docs just need to be updated.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-02-25 23:07:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/103#issuecomment-1051344723  

Do we have tests for it?

---

## Comment 3

> Username: cmazakas  
> Created at: 2022-02-25 23:12:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/103#issuecomment-1051347488  

Yup, those tests live here:  
https://github.com/boostorg/unordered/blob/0d3ece98c1eaf4305d8422aad6517f8c924ca763/test/unordered/merge_tests.cpp#L285-L333
