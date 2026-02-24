# #61 - `reset()` marked as deprecated [Closed]

> Username: Kojoley  
> Created at: 2018-10-22 23:16:08 UTC  
> Updated at: 2018-10-23 20:49:59 UTC  
> Closed at: 2018-10-23 20:49:59 UTC  
> Url: https://github.com/boostorg/optional/issues/61  

It should be a mistake that `rest()` of `boost::optional` is marked as deprecated because `std::optional` has `reset()` [\[optional.mod\]](http://eel.is/c++draft/optional.mod).  
  
The story behind this finding  
---  
I am working on adding `std::optional` support for Spirit and it requires rewriting of optional handling. Currently it resets optional with `boost::none` and I wanted to generalize this (as I do not see a reason why it should handle boost and std optional differently) because I cannot reset `std::optional` with `boost::none_t`. The `reset()` method serves perfectly for this purpose, but the problem is that for some reason `boost::optional` has `reset()` marked as deprecated.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-10-23 06:23:43 UTC  
> Url: https://github.com/boostorg/optional/issues/61#issuecomment-432109593  

Yes, I sympathize with the need. I cannot merge your PR as-is. Since deprecation a maintenance thereof has also been abandoned.  It needs unit-testing, marking as `noexcept`, support in `optional<T&>`.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-23 20:47:54 UTC  
> Url: https://github.com/boostorg/optional/issues/61#issuecomment-432413572  

> It needs unit-testing  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/test/optional_test.cpp#L148-L160  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/test/optional_test.cpp#L674-L699  
  
> marking as `noexcept`  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/include/boost/optional/optional.hpp#L383  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/include/boost/optional/detail/optional_trivially_copyable_base.hpp#L132  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/include/boost/optional/detail/old_optional_implementation.hpp#L337  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/include/boost/optional/detail/optional_reference_spec.hpp#L159  
  
> support in `optional<T&>`  
  
https://github.com/boostorg/optional/blob/106f9aee5e574a092a06ae0cb3446b587c2eabb8/include/boost/optional/detail/optional_reference_spec.hpp#L159

---

## Comment 3

> Username: akrzemi1  
> Created at: 2018-10-23 20:49:37 UTC  
> Url: https://github.com/boostorg/optional/issues/61#issuecomment-432414152  

Right. Sorry for too shallow an investigation on my side. I merged the PR with some minor modifications in the documentation.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-23 20:49:59 UTC  
> Url: https://github.com/boostorg/optional/issues/61#issuecomment-432414262  

Thanks!
