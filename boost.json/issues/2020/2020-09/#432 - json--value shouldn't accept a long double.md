# #432 - json::value shouldn't accept a long double [Closed]

> Username: hadrielk  
> Created at: 2020-09-29 12:03:12 UTC  
> Updated at: 2020-10-04 19:28:42 UTC  
> Closed at: 2020-10-04 19:28:42 UTC  
> Url: https://github.com/boostorg/json/issues/432  

In `json::value`'s constructor and assignment operator, it accepts a `long double` and `static_cast`'s it to a `double`.  
  
I believe that technically can lead to a loss of precision on certain platforms, no?  
  
If so, it should be removed - otherwise you'd be silently ignoring conversion warnings/errors from the compiler, which the user may want to see and/or prevent.  
  
And it should be removed from initializer-list support via `json::value_ref`, and from `detail::max_align_t`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-04 00:17:56 UTC  
> Url: https://github.com/boostorg/json/issues/432#issuecomment-703180491  

> And it should be removed from ... detail::max_align_t.  
  
Well no, that wouldn't be a good idea, because `detail::max_align_t` is used in the implementation of `monotonic_resource`, which is general purpose and not necessarily tied to `json::value`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-04 00:18:16 UTC  
> Url: https://github.com/boostorg/json/issues/432#issuecomment-703180514  

Otherwise, I agree that users should have to opt-in to narrowing conversions.
