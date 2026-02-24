# #19 Feature/remove deduce scalar and assign from cmp [Closed]

> Username: agate-pris  
> Created at: 2019-08-03 10:08:00 UTC  
> Updated at: 2019-08-06 09:19:54 UTC  
> Closed at: 2019-08-06 09:19:54 UTC  
> Url: https://github.com/boostorg/qvm/pull/19  

Fix #18

---

## Comment 1

> Username: zajo  
> Created_at: 2019-08-04 01:10:24 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-517965648  

I don't get the point of `equal_to`. Isn't it equivalent to `op==`?

---

## Comment 2

> Username: agate-pris  
> Created_at: 2019-08-04 07:21:19 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-517980500  

This is actually equivalent to `operator==`. The `cmp` test uses `std::equal_to`, but the old standard library does not have a specialization for `void` which allows objects of different types to be compared. In order to make it possible, I added `boost::qvm::qvm_detail::equal_to` this time.  
  
I think that implementations of `operator==` and `operator! =` (For vec, mat and quaternion) should be replaced with `cmp` to eliminate code duplication. I will use this implementation for that too.

---

## Comment 3

> Username: zajo  
> Created_at: 2019-08-05 02:13:15 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518061021  

I merged your changes but also made edits. I agree that `deduce_scalar` was a bad idea, but I removed `detail/equal_to`. Please let me know if the latest `develop` addresses the issues you had.

---

## Comment 4

> Username: agate-pris  
> Created_at: 2019-08-06 03:17:59 UTC  
> Updated_at: 2019-08-06 03:18:31 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518481159  

Thanks for merging, zajo. However, your changes to `cmp` for quat does not support non copy constructible scalar types.

---

## Comment 5

> Username: agate-pris  
> Created_at: 2019-08-06 05:39:23 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518510092  

[This](https://github.com/agate-pris/qvm/pull/2/files) is my change to `operator==`. I changed only to reuse "cmp" and remove code duplication. However, it requires deducing argument types to "std::equal_to", so it only works in C++14 or later. This is why I made `qvm_detail::equal_to`.

---

## Comment 6

> Username: zajo  
> Created_at: 2019-08-06 05:52:23 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518513614  

https://boostorg.github.io/qvm/#scalar_requirements  
  
Are these not practical for you? What scalar type wouldn't be copyable?

---

## Comment 7

> Username: agate-pris  
> Created_at: 2019-08-06 08:44:31 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518573275  

It is completely practical for me. I'm sorry, Thank you!

---

## Comment 8

> Username: zajo  
> Created_at: 2019-08-06 09:19:54 UTC  
> Url: https://github.com/boostorg/qvm/pull/19#issuecomment-518586639  

So, to clarify, the call to `assign` in vector/matrix `cmp` is now removed. It was invoked in order to facilitate easier debugging (since the elements are pulled into local variables), but that also invoked the scalar's default constructor, which could have overhead with user-defined scalars. Thanks!

---
