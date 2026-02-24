# #32 Fix: Return rvalue-ref to stored_ in rref-qualified deref-operators [Merged]

> Username: vector-of-bool  
> Created at: 2021-11-21 03:34:58 UTC  
> Updated at: 2021-11-21 06:46:10 UTC  
> Merged at: 2021-11-21 06:46:07 UTC  
> Closed at: 2021-11-21 06:46:07 UTC  
> Url: https://github.com/boostorg/leaf/pull/32  

`result::value()` correctly returns `std::move(stored_)`, but `operator*` simply returns `value()`, which will always call the lref-qualified `value()`, and incorrectly attempt to bind the returned (lvalue reference) to an rvalue reference.  
  
This change calls `move(*this)` for an rvalue call to `value()`

---

## Comment 1

> Username: zajo  
> Created_at: 2021-11-21 06:46:10 UTC  
> Url: https://github.com/boostorg/leaf/pull/32#issuecomment-974764608  

Thank you! I should add unit tests for this.

---
