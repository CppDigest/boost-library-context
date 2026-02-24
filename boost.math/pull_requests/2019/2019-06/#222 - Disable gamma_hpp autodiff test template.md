# #222 Disable gamma_hpp autodiff test template. [Merged]

> Username: pulver  
> Created at: 2019-06-28 22:33:58 UTC  
> Updated at: 2019-06-29 19:05:04 UTC  
> Merged at: 2019-06-29 12:20:22 UTC  
> Closed at: 2019-06-29 12:20:22 UTC  
> Url: https://github.com/boostorg/math/pull/222  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-06-29 00:39:13 UTC  
> Url: https://github.com/boostorg/math/pull/222#issuecomment-506912446  

Why comment it out instead of just deleting it?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-06-29 12:24:31 UTC  
> Url: https://github.com/boostorg/math/pull/222#issuecomment-506953227  

I don't quite understand what happened here. It looks like the `autodiff` type was being passed into the `float_distance`, which is clearly undefined, but why was it being passed into that function?

---

## Comment 3

> Username: kedarbhat  
> Created_at: 2019-06-29 18:40:55 UTC  
> Updated_at: 2019-06-29 18:46:03 UTC  
> Url: https://github.com/boostorg/math/pull/222#issuecomment-506978550  

Hi Nick, `float_distance` works for `autodiff_fvar`; this is part of the next_hpp tests in test_autodiff_8.cpp.   
  
The errors in the gamma function tests were due to infinity valued instances of `autodiff_fvar` were being passed into `float_distance`, at least that's the way I'm reading the  error below (please correct me if you're looking at a different error).  
  
I've submitted a PR to Matthew (in the `pulver/autodiff` repo) for possible inclusion after the Boost 1.71 release, so that thrown exceptions are caught and the inputs which caused the exception are printed.   
  
_unknown location(0): fatal error: in "test_autodiff_7/gamma_hpp<float>": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<std::domain_error>  
std::exception::what: Error in function float_distance<N5boost4math15differentiation11autodiff_v16detail4fvarIfLm5EEE>(N5boost4math15differentiation11autodiff_v16detail4fvarIfLm5EEE, N5boost4math15differentiation11autodiff_v16detail4fvarIfLm5EEE): Argument a must be finite, but got depth(1)(inf,0,0,0,0,0)_

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-06-29 19:02:24 UTC  
> Url: https://github.com/boostorg/math/pull/222#issuecomment-506979879  

> I've submitted a PR to Matthew (in the pulver/autodiff repo) for possible inclusion after the Boost 1.71 release, so that thrown exceptions are caught and the inputs which caused the exception are printed  
  
That's just in the tests, correct?

---

## Comment 5

> Username: kedarbhat  
> Created_at: 2019-06-29 19:05:03 UTC  
> Url: https://github.com/boostorg/math/pull/222#issuecomment-506980005  

Correct, the library itself remains untouched.

---
