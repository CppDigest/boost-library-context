# #72 PR66: Moved to here for integration testing. [Merged]

> Username: jzmaddock  
> Created at: 2017-06-25 10:45:19 UTC  
> Updated at: 2019-11-02 11:23:15 UTC  
> Merged at: 2017-08-23 17:47:46 UTC  
> Closed at: 2017-08-23 17:47:46 UTC  
> Url: https://github.com/boostorg/math/pull/72  



---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2017-08-04 22:20:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/72#pullrequestreview-54469368  

📁 include/boost/math/quadrature/detail/tanh_sinh_detail.hpp

```diff
 411 |+    {
 412 |+       Real t = h * i;
 413 |+       if ((first_complement < 0) && (t < m_t_crossover))
```

> Username: NAThompson  
> Created_at: 2017-08-04 20:25:35 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131479286  

Since `first_complement` is a `std::size_t`, then the condition `first_complement < 0` always evaluates to false. So this check can be removed?

> Username: jzmaddock  
> Created_at: 2017-08-06 12:02:42 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131541294  

Ugh... that logic is all wrong.... the "problem" is that if there's a bug in the first row's values the error generated gradually gets erased by subsequent rows so I didn't spot it.  Will fix by using the same logic used by subsequent rows (which are thankfully correct!)

---

📁 include/boost/math/quadrature/detail/tanh_sinh_detail.hpp

```diff
  16 |+ #include <boost/math/special_functions/next.hpp>
  17 |+ 
  18 |+ 
```

> Username: NAThompson  
> Created_at: 2017-08-04 20:53:17 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131484338  

Add:  
  
    #ifdef __GNUC__  
    #pragma GCC diagnostic ignored "-Wliteral-range"  
    #endif  
  
here to remove underflow warnings.

> Username: jzmaddock  
> Created_at: 2017-08-06 08:56:03 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131537843  

I keep trying to find a way to make this work, with GCC-6.3 and:  
  
#ifdef __GNUC__  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wliteral-range"  
#pragma GCC diagnostic ignored "-Woverflow"  
#endif  
  
I still see:  
  
In file included from ..\..\../boost/math/quadrature/tanh_sinh.hpp:35:0,  
                 from tanh_sinh_quadrature_test.cpp:17:  
..\..\../boost/math/quadrature/detail/tanh_sinh_detail.hpp:561:7: warning: floating constant truncated to zero [-Woverflow]  
  
Yet strangely, -Wno-overflow does silence things when use don the command line :(  
  
Using #pragama GCC system_header seems to work though - not ideal but it will have to do I guess.


📁 test/tanh_sinh_quadrature_test.cpp

```diff
 206 |+     Real err;
 207 |+     Real L1;
 208 |+     Real Q = integrator.integrate(f, &err, &L1, 0, boost::math::tools::epsilon<Real>(), boost::math::tools::epsilon<Real>(), tol);
```

> Username: NAThompson  
> Created_at: 2017-08-04 20:39:13 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131481803  

This doesn't compile for me using `g++ 6.3.0`. I added this test of the `detail` to diagnose some bug I was hitting, but now I'm not sure that it is relevant since there are so many other tests. Should it just be removed?

> Username: jzmaddock  
> Created_at: 2017-08-06 08:44:48 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131537552  

Ugh, not sure how that slipped through, will remove as suggested.

---

📁 test/tanh_sinh_quadrature_test.cpp

```diff
 589 |+ {
 590 |+    BOOST_MATH_STD_USING
 591 |+    std::cout << "Testing 2 argument functiors on type " << boost::typeindex::type_id<Real>().pretty_name() << "\n";
```

> Username: NAThompson  
> Created_at: 2017-08-04 20:39:56 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131481946  

Trivial spelling error on `functiors`.

> Username: jzmaddock  
> Created_at: 2017-08-06 11:49:50 UTC  
> Updated_at: 2017-08-21 12:36:17 UTC  
> Url: https://github.com/boostorg/math/pull/72#discussion_r131541060  

Oops, thanks for spotting that!


---
