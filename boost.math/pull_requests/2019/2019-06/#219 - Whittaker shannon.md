# #219 Whittaker shannon [Merged]

> Username: NAThompson  
> Created at: 2019-06-20 11:21:39 UTC  
> Updated at: 2019-06-26 10:59:04 UTC  
> Merged at: 2019-06-26 10:58:27 UTC  
> Closed at: 2019-06-26 10:58:27 UTC  
> Url: https://github.com/boostorg/math/pull/219  

This was part of the Daubechies wavelets PR, but that one was getting just huge, and hence I figured I'd split it into more digestible chunks.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-06-22 19:02:17 UTC  
> Url: https://github.com/boostorg/math/pull/219#issuecomment-504690684  

@pulver : Do you mind taking a look at the `.prime()` function on the Whittaker-Shannon derivative? I think if you wrote the automatic differentiation formula for evaluation of the derivative it would be way more accurate than the braindead evaluation that I'm using here.

---

## Comment 2

> Username: pulver  
> Created_at: 2019-06-24 13:03:53 UTC  
> Updated_at: 2019-06-24 14:42:35 UTC  
> Url: https://github.com/boostorg/math/pull/219#issuecomment-505000677  

> @pulver : Do you mind taking a look at the `.prime()` function on the Whittaker-Shannon derivative? I think if you wrote the automatic differentiation formula for evaluation of the derivative it would be way more accurate than the braindead evaluation that I'm using here.  
  
Is there a reason why `operator()` can't be written as a template function, so that it can be called with a parameter of type `autodiff_fvar<double,1>`? The return value `y` will then have the derivative via `y.derivative(1)`. Of course the method will need to handle types that conform to the [Conceptual Requirements for Real Number Types](https://www.boost.org/libs/math/doc/html/math_toolkit/real_concepts.html).  
  
The alternative of writing a general autodiff overload, as was done with `lambert_w0`, requires the calculation of all derivatives up to any given order, which would be a bit of an overkill if you're only interested in the first.

---

## Comment 3

> Username: pabristow  
> Created_at: 2019-06-24 14:29:38 UTC  
> Url: https://github.com/boostorg/math/pull/219#issuecomment-505035091  

If you chaps are thinking about a change here, you might consider putting a warning in the code and the docs that this is subject to further work-in-progress.  So anyone using it is forewarned that they might have to revisit their code later (and it will be a breaking change). Just a thought.  (Either seem reasonable but avoiding unwanted computation is never bad).

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-06-24 15:39:45 UTC  
> Url: https://github.com/boostorg/math/pull/219#issuecomment-505064989  

I got it working; basically I just following the autodiff algorithm through by hand.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2019-06-26 10:58:13 UTC  
> Url: https://github.com/boostorg/math/pull/219#issuecomment-505826516  

CI failure was a travis problem; shouldn't show up again.

---
