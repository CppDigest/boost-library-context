# #1060 Fix for msvc defining _Complex_I in complex.h [Merged]

> Username: mborland  
> Created at: 2023-12-21 12:22:07 UTC  
> Updated at: 2023-12-22 12:28:40 UTC  
> Merged at: 2023-12-22 12:28:37 UTC  
> Closed at: 2023-12-22 12:28:37 UTC  
> Url: https://github.com/boostorg/math/pull/1060  

@jzmaddock. I think pushing and popping the value of "I" seems to be the safest workaround unless you have a better idea.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-12-21 16:55:19 UTC  
> Url: https://github.com/boostorg/math/pull/1060#issuecomment-1866642501  

I wonder what the best way to fix this is?  A quick grep shows the following files effected:  
  
tools/polynomial.hpp  
tools/mp.hpp  
special_functions/detail?bessel_ik.hpp (uses variables named I, J, K)  
special_functsions/bessel.hpp  
bindings/rr.hpp  
bindings/mpreal.hpp  
bindings/mpfr.hpp  
  
Plus lots of occurrences of "I" in comments which we can ignore.  
  
BTW I used special_functions.hpp purely as a sledge hammer to try and get a quick reproducer, it would be better to fix the individual files.  
  
Also your fix is msvc specific, and I can reproduce with gcc, which outputs thousands of errors along the lines of:  
  
```  
In file included from /usr/lib/gcc/x86_64-pc-cygwin/11/include/c++/complex.h:38,  
                 from t.cpp:1:  
./boost/math/tools/mp.hpp:171:34: error: expected ‘>’ before numeric constant  
  171 | template<typename L, std::size_t I>  
      |                                  ^  
In file included from ./boost/math/policies/policy.hpp:11,  
                 from ./boost/math/special_functions/math_fwd.hpp:31,  
                 from ./boost/math/special_functions/airy.hpp:11,  
                 from ./boost/math/special_functions.hpp:15,  
                 from t.cpp:2:  
./boost/math/tools/mp.hpp:172:46: error: could not convert ‘(__complex__ float){0.0f, 1.0e+0f}’ from ‘__complex__ float’ to ‘long unsigned int’  
  172 | using mp_at_c = typename detail::mp_at_c<L, I>::type;  
      |                                              ^  
      |                                              |  
      |                                              __complex__ float  
./boost/math/tools/mp.hpp:172:47: error: ‘<declaration error>’ is not a template [-fpermissive]  
  172 | using mp_at_c = typename detail::mp_at_c<L, I>::type;  
      |                                               ^~  
In file included from /usr/lib/gcc/x86_64-pc-cygwin/11/include/c++/complex.h:38,  
                 from t.cpp:1:  
./boost/math/tools/mp.hpp:174:31: error: expected nested-name-specifier before numeric constant  
  174 | template<typename L, typename I>  
```  
  
So my temptation is to replace `I` with `Integer` for template type names, that just leaves the bessel functions which use the mathematically correct symbols I, J and K for variable names (lower case names mean something else in this context).  It's not pretty but maybe change to `result_I`, `result_J` and `result_K` ?

---

## Comment 2

> Username: mborland  
> Created_at: 2023-12-21 17:01:06 UTC  
> Url: https://github.com/boostorg/math/pull/1060#issuecomment-1866650162  

Yeah CI shows that Cygwin and GCC running in GNU mode all do the wrong thing. Renaming everything works. The meta tools can have Index instead of I.

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2023-12-22 11:33:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1060#pullrequestreview-1794362874  

📁 doc/internals/polynomial.qbk

```diff
  24 |-       template <class I>
  24 |+       template <class Iterator>
  25 |       polynomial(I first, I last);
```

> Username: jzmaddock  
> Created_at: 2023-12-22 11:33:44 UTC  
> Url: https://github.com/boostorg/math/pull/1060#discussion_r1434982037  

Doesn't this line also need changing?


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2023-12-22 11:35:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1060#pullrequestreview-1794364520  

📁 include/boost/math/special_functions/detail/bessel_ik.hpp

```diff
  79 | 
  80 |- // Calculate K(v, x) and K(v+1, x) by method analogous to
  80 |+ // Calculate result_K(v, x) and result_K(v+1, x) by method analogous to
```

> Username: jzmaddock  
> Created_at: 2023-12-22 11:35:22 UTC  
> Url: https://github.com/boostorg/math/pull/1060#discussion_r1434983062  

Nit pick: K here refers to the Bessel function K, not to our variable, so I think we should leave the comment unchanged.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2023-12-22 11:36:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1060#pullrequestreview-1794365147  

📁 include/boost/math/special_functions/detail/bessel_ik.hpp

```diff
 202 | 
 203 |- // Calculate K(v, x) and K(v+1, x) by evaluating continued fraction
 203 |+ // Calculate result_K(v, x) and result_K(v+1, x) by evaluating continued fraction
```

> Username: jzmaddock  
> Created_at: 2023-12-22 11:36:00 UTC  
> Url: https://github.com/boostorg/math/pull/1060#discussion_r1434983453  

Likewise, comment refers to the special function K.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2023-12-22 11:37:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1060#pullrequestreview-1794367010  

📁 include/boost/math/special_functions/detail/bessel_ik.hpp

```diff
 376 |     T scale_sign = 1;
 377 |-     for (k = 1; k <= n; k++)                   // forward recurrence for K
 377 |+     for (k = 1; k <= n; k++)                   // forward recurrence for result_K
```

> Username: jzmaddock  
> Created_at: 2023-12-22 11:37:54 UTC  
> Url: https://github.com/boostorg/math/pull/1060#discussion_r1434984575  

Again K refers to the mathematical function not our variable... life would be easier if mathematicians didn't keep reusing the same names over and over!


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-12-22 11:39:23 UTC  
> Url: https://github.com/boostorg/math/pull/1060#issuecomment-1867585605  

Many thanks Matt for taking this on, some mostly nit picking comments above!

---
