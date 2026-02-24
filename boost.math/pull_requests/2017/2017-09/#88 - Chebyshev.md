# #88 Chebyshev [Merged]

> Username: NAThompson  
> Created at: 2017-09-13 23:59:40 UTC  
> Updated at: 2018-02-09 23:34:36 UTC  
> Merged at: 2017-09-26 18:07:19 UTC  
> Closed at: 2017-09-26 18:07:19 UTC  
> Url: https://github.com/boostorg/math/pull/88  

This is a first draft of the Chebyshev transform. Since the Chebyshev transform relies on the discrete cosine transform, I have put a dependency of FFTW3 in `boost/math/special_functions/chebyshev_transform.hpp`, and hence is must be linked with the flags `-lfftw3` if you use the double precision template instantiation, `-lfftw3f` for the float precision template instantiation, and `-lfftw3l` for the long double template instantiation. Ostensibly it could work in `__float128` precision (see [here](http://www.fftw.org/fftw3_doc/Precision.html) but I was unable to figure out how to make it compile if `__float128` isn't supported on the platform at all.  
  
I used a C++17 `if constexpr` to simulate template metaprogramming on the `fftw_plan`. I think this is ugly and I think there's a better way, so if someone could take a look and see what can be done I'd appreciate it!  
  
The numerical derivative works well, but does not have asymptotically optimal scaling, and I also need to be able expand the number of places where the function can be evaluated in case no coefficients are smaller than the tolerance. (Right now, they are only truncated.)  
  
So, more work to do, as always, but I think this is enough to get the discussion going esp. with regard to the dependency on FFTW.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2017-09-14 12:21:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/88#pullrequestreview-62730180  

📁 include/boost/math/special_functions/chebyshev_transform.hpp

```diff
  20 |+ public:
  21 |+     template<class F>
  22 |+     chebyshev_transform(const F& f, Real a, Real b, Real tol=500*std::numeric_limits<Real>::epsilon()): m_a(a), m_b(b)
```

> Username: jzmaddock  
> Created_at: 2017-09-14 12:21:37 UTC  
> Updated_at: 2017-09-26 17:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/88#discussion_r138876726  

Nick, I think the simplest thing to do here is to add a final enable_if argument:  
  
`chebyshev_transform(const F& f, Real a, Real b, Real tol=500*std::numeric_limits<Real>::epsilon(), typename std::enable_if<std::is_same<double, Real>::value>::type* = nullptr)`  
  
Then this constructor can only be called if type Real is a double, repeat for float and long double and you're done and the if constexpr can go.  It does mean some code duplication, but it's not a huge function so probably not an issue?

> Username: NAThompson  
> Created_at: 2017-09-17 02:00:06 UTC  
> Updated_at: 2017-09-26 17:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/88#discussion_r139299495  

Yup, will try to get this done!


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2017-09-14 12:24:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/88#pullrequestreview-62730826  

📁 test/chebyshev_test.cpp

```diff
  77 |+ 
  78 |+ template<class Real>
  79 |+ void test_sin_chebyshev_transform()
```

> Username: jzmaddock  
> Created_at: 2017-09-14 12:24:23 UTC  
> Updated_at: 2017-09-26 17:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/88#discussion_r138877282  

Nick, can we as with the header, split this into 2 tests: one that depends on fftw3 and one that doesn't?  Don't worry about the Jamfile, I can hook the one with the external dependency to only run when fftw3 is available.  
  
Other than that, this looks pretty much ready to go to me!

> Username: NAThompson  
> Created_at: 2017-09-17 01:59:28 UTC  
> Updated_at: 2017-09-26 17:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/88#discussion_r139299481  

Fixed!


---

## Comment 3

> Username: NAThompson  
> Created_at: 2017-09-17 22:27:28 UTC  
> Updated_at: 2017-09-17 22:28:14 UTC  
> Url: https://github.com/boostorg/math/pull/88#issuecomment-330098490  

Ok, now I just need to make the constructor adaptive so that if we don't pick a large enough number of Chebyshev points, we can expand it. Also should consider doing an indefinite integration routine, using the equation given [here](https://en.wikipedia.org/wiki/Chebyshev_polynomials#Differentiation_and_integration). Then we just copy-paste the constructor to make it work in float, long double, and quad, and it should be ready to go.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-09-19 06:52:17 UTC  
> Url: https://github.com/boostorg/math/pull/88#issuecomment-330446624  

I couldn't get the `enable_if` to work; kept on getting this error once I added the other constructors:  
  
    /include/boost/math/special_functions/chebyshev_transform.hpp:24:49:   
    error: no type named 'type' in 'std::__1::enable_if<false, void>'; 'enable_if' cannot be used to disable this declaration  
    typename std::enable_if<std::is_same<double, Real>::value>::type* = nullptr)  
  
But presumably this is a small problem; other than this I think it's ready for review.

---
