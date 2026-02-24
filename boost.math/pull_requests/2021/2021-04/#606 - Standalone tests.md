# #606 Standalone tests [Merged]

> Username: mborland  
> Created at: 2021-04-09 19:37:55 UTC  
> Updated at: 2021-04-25 08:38:05 UTC  
> Merged at: 2021-04-22 18:44:22 UTC  
> Closed at: 2021-04-22 18:44:22 UTC  
> Url: https://github.com/boostorg/math/pull/606  

Adds missing include tests and fixes for failures of the former. Tests run by regular CI using Jamfile and standalone CI using CMake.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2021-04-10 11:40:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-632913829  

📁 include/boost/math/distributions/arcsine.hpp

```diff
  88 |         {
  89 |+           #ifndef BOOST_MATH_STANDALONE
  90 |           std::string msg = "x_max argument is %1%, but must be > x_min = " + lexical_cast<std::string>(x_min) + "!";
```

> Username: NAThompson  
> Created_at: 2021-04-10 11:40:55 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r611034576  

@jzmaddock : Does this still need lexical_cast? It looks like we could just get away with `std::to_string`. . .

> Username: jzmaddock  
> Created_at: 2021-04-11 18:39:06 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r611229052  

+1, I think that's correct, yes.  And it allows us to remove the #if logic entirely.

> Username: mborland  
> Created_at: 2021-04-11 18:47:35 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r611230058  

`std::to_string` will only work with the builtin arithmetic types. I can keep the else block which will pass tests using `real_concept` but offer a less helpful error message.

> Username: jzmaddock  
> Created_at: 2021-04-11 18:49:18 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r611230308  

Good point, glad one of us is paying attention! :)


---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-10 13:50:25 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-817139829  

@NAThompson Do you know why [this error ](https://github.com/boostorg/math/runs/2309694662?check_suite_focus=true#step:18:217)has started popping up? I first saw it on [this PR](https://github.com/boostorg/math/pull/604) the other day.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-04-10 14:06:56 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-817142167  

@mborland : That's generally due to some minor variations in rounding modes, or some multiprecision diffs of no real consequence  
  
Hopefully fixed in 2553ecf00ee234d7d5.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-04-11 18:45:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-817353993  

Really big thanks for this Matt: there's some good stuff here, and lot's of errors caught and fixed which is great!  
  
One busy-body comment: the new files really should have your name on the copyright and the current date on them ;)  
  
One more substantive comment: some of the include tests don't do anything except #include the header: unfortunately I've found from bitter experience that that is not enough to catch all errors, to do that, you actually have to instantiate the templates as well.  This one has come up on the mailing list several times BTW, where someone suggests automating producing #include tests, but sadly this only catches errors in the interface, not for the most part, in the templates themselves.  
  
We could push that to the long grass for a moment and merge this as is for now, but it might be easier to spot which tests need to be updated while the PR is around?

---

## Comment 5

> Username: mborland  
> Created_at: 2021-04-11 19:00:18 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-817355940  

> We could push that to the long grass for a moment and merge this as is for now, but it might be easier to spot which tests need to be updated while the PR is around?  
  
I think it would be easier to just keep going against this PR. It makes sense you need to instantiate the templates to get better results.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-04-17 16:28:18 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-821849555  

Pending further review I believe this is good to go now.

---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2021-04-17 16:46:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638241384  

📁 include/boost/math/quadrature/gauss.hpp

```diff
  83 |    {
  83 |-       static const std::array<T, 4> data = {
  84 |+       static constexpr std::array<T, 4> data = {
```

> Username: NAThompson  
> Created_at: 2021-04-17 16:46:19 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615276424  

Yup that's a good diff to make. But should it also be `const`? ie. `static constexpr const`?

> Username: mborland  
> Created_at: 2021-04-17 16:53:09 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615277207  

In this situation no; you only need `constexpr const` for reference variables.


---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2021-04-17 16:47:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638241473  

📁 include/boost/math/tools/engel_expansion.hpp

```diff
  62 |             // Sanity check: This should only happen when wraparound occurs:
  62 |-             if (a_[i] < a_[i-1])
  63 |+             if (a_[j] < a_[j-1])
```

> Username: NAThompson  
> Created_at: 2021-04-17 16:47:47 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615276621  

Did I accidently alias `i` in a nested loop?

> Username: mborland  
> Created_at: 2021-04-17 16:55:20 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615277404  

The change suppressed shadowing warnings because `i` is [defined here](https://github.com/boostorg/math/blob/63a16971afc470e1772ed99da2ed2a2835a8954c/include/boost/math/tools/engel_expansion.hpp#L41).


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2021-04-17 16:48:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638241503  

📁 include/boost/math/tools/header_deprecated.hpp

```diff
   9 | #ifdef _MSC_VER
  10 | // Expands to "This header is deprecated; use expr instead."
  11 |- #define BOOST_MATH_HEADER_DEPRECATED(expr) #prama message("This header is deprecated; use " expr " instead.")
```

> Username: NAThompson  
> Created_at: 2021-04-17 16:48:13 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615276678  

Shouldn't the fix have been `#pragma` to remove the `#prama` typo?

> Username: mborland  
> Created_at: 2021-04-17 16:59:26 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615277941  

That may have worked. In the GCC case the spelling was right but the `#warning` did not expand correctly so I used `_Pragma` instead


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2021-04-17 16:48:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638241541  

📁 include/boost/math/tools/luroth_expansion.hpp

```diff
  42 |-         Real computed = dn;
  41 |+         x = x - dn1;
  42 |+         Real computed = dn1;
```

> Username: NAThompson  
> Created_at: 2021-04-17 16:48:49 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615276755  

I'm confused about what I did on this one that needed the variable name change.

> Username: mborland  
> Created_at: 2021-04-17 17:00:21 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615278045  

`dn` is defined [again here](https://github.com/boostorg/math/blob/63a16971afc470e1772ed99da2ed2a2835a8954c/include/boost/math/tools/luroth_expansion.hpp#L52). Changed the name to suppress shadowing warnings.


---

## Comment 11

> Username: NAThompson  
> Created_at: 2021-04-17 16:50:07 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-821852459  

I added some trivial comments; I think it's looking pretty good. @jzmaddock will have an orthogonal set of suggestions but I'm happy.

---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:00:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638264187  

📁 test/compile_test/cstdfloat_cmath_incl_test.cpp

```diff
  16 |+ void compile_and_link_test()
  17 |+ {
  18 |+     #ifdef __float128
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:00:23 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615286526  

__float128 is a type not a macro?  
  
Looking back at our docs on this, they look to be a bit confused, or indeed flat out wrong in places, but I *think* the thing to do is if BOOST_FLOAT128_C is defined, then `boost::float128` is available and can be tested as you have here.


---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:01:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638264257  

📁 test/compile_test/cstdfloat_limits_incl_test.cpp

```diff
  16 |+ void compile_and_link_test()
  17 |+ {
  18 |+     #ifdef __float128
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:01:30 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615286604  

as above __float128 is not a macro.


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:02:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638264335  

📁 test/compile_test/interpolators_cardinal_trig_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/cardinal_trigonometric.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:02:49 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615286711  

Do we still need to instantiate something here?


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:03:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638264361  

📁 test/compile_test/interpolators_cubic_hermite_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/cubic_hermite.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:03:14 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615286746  

Also need to instantiate something here?


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:10:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638264965  

📁 test/compile_test/interpolators_makima_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/makima.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:10:44 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615287618  

Instantiation?

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:08 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615287944  

Something like:  
  
```  
// we can't help but include a std lib header here:  
#include <vector>  
  
int main()  
{  
  std::vector<double> x{1, 5, 9 , 12};  
  std::vector<double> y{8,17, 4, -3};  
  using boost::math::interpolators::makima;  
  makima<std::vector<double>> spline(std::move(x), std::move(y));  
  // evaluate at a point:  
  double z = spline(3.4);  
  // evaluate derivative at a point:  
  double zprime = spline.prime(3.4);  
}  
```  
  
Which is straight out of the docs, might do it - I hope!!  
  
The other interpolators all have the same interface... so hopefully just change the name ;)

> Username: mborland  
> Created_at: 2021-04-18 10:28:11 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615377522  

I should have mentioned I skipped the interpolators. I am sure they could be made to work with a c-array or pointers, but not sure it was worth the effort. Adding `std::vector` should work just fine.


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265247  

📁 test/compile_test/interpolators_pchip_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/pchip.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:26 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615287982  

Instantiation?


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265255  

📁 test/compile_test/interpolators_quintic_hermite_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/quintic_hermite.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:34 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615287999  

Instantiation?


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265266  

📁 test/compile_test/interpolators_septic_hermite_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/septic_hermite.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:43 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288017  

Instantiation?


---

## Review 20 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265278  

📁 test/compile_test/interpolators_vector_barycentric_rational_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/vector_barycentric_rational.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:15:56 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288034  

Instantiation?

> Username: mborland  
> Created_at: 2021-04-18 10:52:07 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615380358  

@NAThompson `vector_barycentric_rational` is the only one of the interpolators not in the `interpolators` namespace. Do you want me to move it or leave it? Currently it is in just `boost::math`.

> Username: NAThompson  
> Created_at: 2021-04-19 14:06:49 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615880064  

Yes, that was a screw up on my part.


---

## Review 21 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:16:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265286  

📁 test/compile_test/interpolators_whittaker_shannon_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/interpolators/whittaker_shannon.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:16:06 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288060  

Instantiation?


---

## Review 22 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:17:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265342  

📁 test/compile_test/quad_exp_sinh_incl_test.cpp

```diff
   7 | // #includes all the files that it needs to.
   8 | //
   9 | #include <boost/math/quadrature/exp_sinh.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:17:03 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288167  

Instantiation?  
  
Probably just copy from gauss test?

> Username: jzmaddock  
> Created_at: 2021-04-17 18:17:54 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288312  

Ooops ignore that - my bad!!


---

## Review 23 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-17 18:19:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638265532  

📁 test/compile_test/tools_ulps_plot_incl_test.cpp

```diff
   7 |+ // #includes all the files that it needs to.
   8 |+ //
   9 |+ #include <boost/math/tools/ulps_plot.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-04-17 18:19:45 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615288501  

Probably need to instantiate something on this one too?


---

## Comment 24

> Username: jzmaddock  
> Created_at: 2021-04-17 18:21:17 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-821865766  

@mborland : many thanks for all the hard work that's obviously gone into this - I've made some nit-picky comments above for a few of the new tests that don't instantiate anything at present.  Other than that, this is all really good stuff!

---

## Review 25 [Commented]

> Username: mborland  
> Created_at: 2021-04-18 11:56:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/606#pullrequestreview-638322972  

📁 include/boost/math/cstdfloat/cstdfloat_cmath.hpp

```diff
 880 | 
 881 | #if !(defined(_GLIBCXX_USE_FLOAT128) && defined(__GNUC__) && (__GNUC__ >= 7))
 882 |+ #if defined(__clang__) && !(!defined(__STRICT_ANSI__) && defined(_GLIBCXX_USE_FLOAT128)) // workaround for clang using libstdc++
```

> Username: mborland  
> Created_at: 2021-04-18 11:55:51 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r615388316  

@jzmaddock I am not sure if this is correct. `std_abs.h` defines `std::abs(__float128)` and clang throws an error for collision that GCC does not. `!(!defined(__STRICT_ANSI__) && defined(_GLIBCXX_USE_FLOAT128))` is just negated of what allows the float128 specialty in libstdc++. Thoughts?

> Username: jzmaddock  
> Created_at: 2021-04-19 19:54:28 UTC  
> Updated_at: 2021-04-21 16:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/606#discussion_r616136021  

It looks reasonably correct - there is some inevitable impiracle guess work here that I guess we just can't avoid.  The tests are all passing though, so yes, looks good to me.


---

## Comment 26

> Username: NAThompson  
> Created_at: 2021-04-19 14:07:08 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-822495139  

All my concerns are resolved.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2021-04-19 19:55:54 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-822743372  

All looks good to me too - @mborland any reason not to merge?

---

## Comment 28

> Username: mborland  
> Created_at: 2021-04-20 16:33:47 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-823423554  

@jzmaddock It have not run a full CI cycle since changing `cstdfloat_cmath` to fix standalone. Made a minor change to cycle to make sure it does not affect older versions of Clang/GCC. Should be good to if this run is clean.

---

## Comment 29

> Username: mborland  
> Created_at: 2021-04-22 15:26:38 UTC  
> Url: https://github.com/boostorg/math/pull/606#issuecomment-824941324  

CI on this is clean again

---
