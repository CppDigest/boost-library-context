# #15 Conditionally unpack an error_id from a thrown system_error [Merged]

> Username: vector-of-bool  
> Created at: 2020-11-21 05:37:23 UTC  
> Updated at: 2020-11-24 00:17:29 UTC  
> Merged at: 2020-11-22 00:47:59 UTC  
> Closed at: 2020-11-22 00:47:59 UTC  
> Url: https://github.com/boostorg/leaf/pull/15  

Previously, if a `std::system_error` was caught and passed to  
`unpack_error_id`, then the `ex.code()` would be unconditionally  
converted to an `error_id`, even if the code did not correspond  
to an actual `error_id` value. This effectively prevents reliable  
use of `std::system_error` as an exception type.  
  
This tweaks to only consider the `system_error` to be an `error_id`  
if the `code()` of that error matches `is_error_id()`.

---

## Comment 1

> Username: zajo  
> Created_at: 2020-11-22 00:53:38 UTC  
> Url: https://github.com/boostorg/leaf/pull/15#issuecomment-731659733  

Thanks! I also added a unit test that confirms that you are correct about the problem. I have to think a bit more and probably expand the unit test before I merge this into other branches (note I made a branch with name identical to the one you made).

---
