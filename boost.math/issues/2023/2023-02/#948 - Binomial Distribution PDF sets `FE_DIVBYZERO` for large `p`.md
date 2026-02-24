# #948 - Binomial Distribution PDF sets `FE_DIVBYZERO` for large `p` [Closed]

> Username: mborland  
> Created at: 2023-02-11 16:31:16 UTC  
> Updated at: 2023-02-13 15:36:58 UTC  
> Closed at: 2023-02-13 15:36:58 UTC  
> Url: https://github.com/boostorg/math/issues/948  

See: https://github.com/scipy/scipy/issues/15101  
  
Confirmed this is the case with this test file: https://gist.github.com/mborland/fd21ed4a53cd1158c14c48f2c5844833  
  
A similar issue for small values of `p` was here: https://github.com/boostorg/math/issues/799

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-02-11 17:08:59 UTC  
> Url: https://github.com/boostorg/math/issues/948#issuecomment-1426827815  

Error is here: https://github.com/boostorg/math/blob/4aac532a8865ab54789f9d776ce79206793fc616/include/boost/math/special_functions/beta.hpp#L376  
  
Fix would be the same as #799.
