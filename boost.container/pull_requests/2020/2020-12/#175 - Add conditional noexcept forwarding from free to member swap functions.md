# #175 Add conditional noexcept forwarding from free to member swap functions. [Merged]

> Username: palebedev  
> Created at: 2020-12-30 05:40:33 UTC  
> Updated at: 2021-01-02 00:04:57 UTC  
> Merged at: 2021-01-02 00:04:26 UTC  
> Closed at: 2021-01-02 00:04:26 UTC  
> Url: https://github.com/boostorg/container/pull/175  

This allows `std::is_nothrow_swappable{,_with}` to work properly.  
This only touches user-facing classes, with the following exceptions:  
- `map`,`set`: only doc changes (with a drive-by missing semicolon fix), `detail::tree` which they derive from already forwards `noexcept` for `swap`.  
- `flat_set`: only doc changes for `flat_set` and a change for `detail::flat_tree`, which it derives from to get `swap`.  
- `static_vector`: while this adds the forwarding, member `static_vector::swap` is currently never `noexcept`, although it could be for noexcept-swappable and noexcept-move-constructible elements.  
- `small_vector`: there is no free swap, and member swap is never `noexcept`, although it could be for noexcept-swappable and noexcept-move-constructible elements. Swapping `small_vector`s of exact same type goes through `std::swap`, which seems to get `noexcept` right, and `small_vector`s of different sizes use `swap` for `boost::container::vector`, which fails to detect when such swap could be `noexcept`. This patch doesn't touch `small_vector`.  
- `scoped_allocator`: neither member nor free `swap` are `noexcept`, although they should be, and all other constructions/assignments/comparisons too, as they only forward these operations to underlying allocators, which are required to not throw exceptions for them. Only `static_vector`'s `static_storage_allocator` might throw for these and it shouldn't appear in `scoped_allocator`. Unsure of the scope of changes needed or whether it is even worth it, this patch doesn't touch `scoped_allocator`.  
  
I am unsure on the last three points, feel free to correct and cherry-pick as needed.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-01-02 00:04:57 UTC  
> Url: https://github.com/boostorg/container/pull/175#issuecomment-753406990  

Many thanks for the patch!

---
