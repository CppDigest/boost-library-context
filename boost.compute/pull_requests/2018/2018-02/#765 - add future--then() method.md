# #765 add future::then() method [Merged]

> Username: lakshayg  
> Created at: 2018-02-20 15:29:19 UTC  
> Updated at: 2018-03-10 08:12:37 UTC  
> Merged at: 2018-03-10 08:12:37 UTC  
> Closed at: 2018-03-10 08:12:37 UTC  
> Url: https://github.com/boostorg/compute/pull/765  

resolves #94

---

## Comment 1

> Username: jszuppe  
> Created_at: 2018-02-27 18:48:39 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-368984397  

Please rebase on `develop`.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-02-27 18:49:01 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-368984520  

Would be great if you add tests for that.

---

## Review 3 [Commented]

> Username: jszuppe  
> Created_at: 2018-02-27 19:04:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/765#pullrequestreview-99813636  

📁 include/boost/compute/async/future.hpp

```diff
  83 |     }
  84 | 
  85 |+     #if defined(BOOST_COMPUTE_CL_VERSION_1_1) || defined(BOOST_COMPUTE_DOXYGEN_INVOKED)
```

> Username: jszuppe  
> Created_at: 2018-02-27 19:04:42 UTC  
> Updated_at: 2018-03-04 11:26:26 UTC  
> Url: https://github.com/boostorg/compute/pull/765#discussion_r171031751  

I think it would be useful to add comment that  
>  The function specified by `callback` must be invokable with zero arguments (e.g. `callback()`).


---

## Comment 4

> Username: lakshayg  
> Created_at: 2018-02-27 19:06:08 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-368989995  

Sure, I can add tests. In which file should I add them?

---

## Comment 5

> Username: jszuppe  
> Created_at: 2018-02-27 19:08:04 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-368990591  

I think `test_event.cpp` is a good place for that.

---

## Comment 6

> Username: coveralls  
> Created_at: 2018-02-28 03:15:20 UTC  
> Updated_at: 2018-03-04 18:20:15 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-369107612  

[![Coverage Status](https://coveralls.io/builds/15799534/badge)](https://coveralls.io/builds/15799534)  
  
Coverage increased (+0.007%) to 82.121% when pulling **a6879d2c2c2aeb8b795fd07b34b843c49e32ab0a on lakshayg:future_then** into **f8aa62c42fc8b25a9edac07861f51983232923f0 on boostorg:develop**.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2018-03-04 10:20:16 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-370217718  

Shouldn't `then()` return reference to modified `future` (`*this`)? OpenCL doesn't guarantee order of execution for event callbacks, but, nonetheless, user may want to register multiple callback to a future. I'd also add `\see_opencl_ref{clSetEventCallback}` and `\opencl_version_warning{1,1}` to the description, so it looks like this:  
  
```  
/// Invokes a generic callback function once the future is ready.  
//  
/// The function specified by callback must be invokable with zero arguments.  
///  
/// \see_opencl_ref{clSetEventCallback}  
/// \opencl_version_warning{1,1}  
```  
  
Otherwise, looks good.

---

## Comment 8

> Username: lakshayg  
> Created_at: 2018-03-04 11:32:55 UTC  
> Url: https://github.com/boostorg/compute/pull/765#issuecomment-370221826  

Done

---
