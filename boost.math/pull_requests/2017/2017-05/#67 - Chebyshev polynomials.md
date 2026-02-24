# #67 Chebyshev polynomials. [Closed]

> Username: NAThompson  
> Created at: 2017-05-17 23:14:54 UTC  
> Updated at: 2018-02-10 01:00:56 UTC  
> Closed at: 2017-09-14 00:00:04 UTC  
> Url: https://github.com/boostorg/math/pull/67  



---

## Review 1 [Commented]

> Username: pabristow  
> Created_at: 2017-05-18 08:28:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/67#pullrequestreview-38870947  

That was quick!  Nice quote. At a glance, looks good.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2017-05-21 11:50:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/67#pullrequestreview-39354608  

📁 include/boost/math/special_functions/chebyshev.hpp

```diff
  26 |+     Real T0 = 1;
  27 |+     Real T1;
  28 |+     if (second)
```

> Username: jzmaddock  
> Created_at: 2017-05-21 11:50:35 UTC  
> Updated_at: 2017-06-30 17:35:56 UTC  
> Url: https://github.com/boostorg/math/pull/67#discussion_r117630855  

To be honest, I think I'd rather have 2 separate functions than switch based on a template parameter - there's not much overlap between the two cases other than the recursion at the end?

> Username: NAThompson  
> Created_at: 2017-05-21 16:32:48 UTC  
> Updated_at: 2017-06-30 17:35:56 UTC  
> Url: https://github.com/boostorg/math/pull/67#discussion_r117636867  

That seems reasonable to me; will make the change.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2017-05-21 11:53:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/67#pullrequestreview-39354651  

📁 include/boost/math/special_functions/chebyshev.hpp

```diff
  72 |+ 
  73 |+ template<class Real>
  74 |+ Real chebyshev_t(unsigned n, Real const & x)
```

> Username: jzmaddock  
> Created_at: 2017-05-21 11:53:24 UTC  
> Updated_at: 2017-06-30 17:35:56 UTC  
> Url: https://github.com/boostorg/math/pull/67#discussion_r117630902  

Paul, can I persuade you to make these forwarders follow the example here: http://www.boost.org/doc/libs/1_64_0/libs/math/doc/html/math_toolkit/special_tut/special_tut_impl.html so that the behaviour of these matches the rest of the lib?  Thanks!


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2017-05-21 11:54:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/67#pullrequestreview-39354683  

📁 doc/math.qbk

```diff
 621 | [include octonion/math-octonion.qbk]
 622 | [include gcd/math-gcd.qbk]
 623 |+ [include interpolators/cubic_b_spline.qbk]
```

> Username: jzmaddock  
> Created_at: 2017-05-21 11:54:14 UTC  
> Updated_at: 2017-06-30 17:35:56 UTC  
> Url: https://github.com/boostorg/math/pull/67#discussion_r117630935  

This seems to be a hangover from something else - that file should be included already (in a different location).


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-05-21 11:56:17 UTC  
> Url: https://github.com/boostorg/math/pull/67#issuecomment-302932185  

Thanks Nick, in addition to the line comments I added, can I get you to hook up testing of this in libs/math/test/compile_test/instantiate.hpp so that our concept checking is enabled for this code?  
  
Thanks!

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-05-21 12:10:49 UTC  
> Url: https://github.com/boostorg/math/pull/67#issuecomment-302932849  

Since you so kindly volunteered to tackled this ;)  I have one other suggestion and a question:  
  
Suggestion: it would also be useful to have access to the roots of the chebyshev polynomials (these are trivially calculated I think?)  
  
Question: Now that we have bumped the polynomial class up to a first class citizen of Boost.Math, is there a use for  
  
template <class T>  
polynomial<T> chebyshev_t_polynomial(int n);  // etc  
  
For accessing the actual coefficients?  Presumably polynomial arithmetic plus the recurrence relation would suffice for that unless there's an easier way?  
  
Best, John.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2017-05-21 16:38:51 UTC  
> Url: https://github.com/boostorg/math/pull/67#issuecomment-302947753  

Yeah, I'll get the compile test and concept checks working. I'm still trying to figure out the convention for the zeros; should we use the "only present non-negative zeros" (which are natural for the Legendre polynomial zeros), or follow the notation used in <a href="https://www.amazon.com/Approximation-Theory-Practice-Applied-Mathematics/dp/1611972396/ref=sr_1_1?ie=UTF8&qid=1495384518&sr=8-1&keywords=approximation+theory+and+approximation+practice">Trefethen</a>.  
  
I have a Chebyshev tranform in the works, which will allow "time-compression" of function evaluation, numerical differentiation, and numerical integration, as a class. This class should satisfy your `etc` in `chebyshev_t_polynomial(int n); // etc`.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2017-09-14 00:00:04 UTC  
> Url: https://github.com/boostorg/math/pull/67#issuecomment-329330493  

New PR elsewhere.

---
