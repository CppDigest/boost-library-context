# #703 Cubic roots [Merged]

> Username: NAThompson  
> Created at: 2021-10-10 17:40:23 UTC  
> Updated at: 2021-10-28 17:27:54 UTC  
> Merged at: 2021-10-27 03:54:30 UTC  
> Closed at: 2021-10-27 03:54:30 UTC  
> Url: https://github.com/boostorg/math/pull/703  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-10-23 21:42:44 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-950219832  

@jzmaddock, @mborland  : Could you take a look?

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2021-10-24 13:59:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/703#pullrequestreview-787515414  

📁 include/boost/math/tools/cubic_roots.hpp

```diff
  29 |+     std::array<Real, 3> roots = {std::numeric_limits<Real>::quiet_NaN(),
  30 |+                                  std::numeric_limits<Real>::quiet_NaN(),
  31 |+                                  std::numeric_limits<Real>::quiet_NaN()};
```

> Username: mborland  
> Created_at: 2021-10-24 13:55:12 UTC  
> Updated_at: 2021-10-24 13:59:24 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735122698  

It does not look like it to me, but is there a scenario where you pass a `NAN` to `std::sort` because there are less than 3 roots? If there is sorting `NANs` is UB.

> Username: NAThompson  
> Created_at: 2021-10-24 15:53:35 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735137262  

Yes, I just passed the nans in to sort, but the comparison returns false and the NaNs retain their place at the end of the array. . .  
  
That's UB??

> Username: NAThompson  
> Created_at: 2021-10-24 17:23:27 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735147136  

Someday I'll become a programmer and learn the UB rules. . .

> Username: jzmaddock  
> Created_at: 2021-10-24 17:28:28 UTC  
> Updated_at: 2021-10-24 17:28:29 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735147657  

Nobody knows the UB rules ;)

> Username: mborland  
> Created_at: 2021-10-26 19:56:27 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r736872718  

> Yes, I just passed the nans in to sort, but the comparison returns false and the NaNs retain their place at the end of the array. . .  
>   
> That's UB??  
  
In the C++20 draft section 25.8.1 [alg.sorting.general] a `Compare` function object is defined and used as `Compare comp` throughout the sorting section with the following requirement for strict weak ordering:  
  
The term strict refers to the requirement of an irreflexive relation (!comp(x, x) for all x), and the term weak to requirements that are not as strong as those for a total ordering, but stronger than those for a partial ordering. If we define equiv(a, b) as !comp(a, b) && !comp(b, a), then the requirements are that comp and equiv both be transitive relations:  
 - comp(a, b) && comp(b, c) implies comp(a, c)  
 - equiv(a, b) && equiv(b, c) implies equiv(a, c)  
  
For doubles as an example !(NAN < x) && !(x < NAN) holds for any x, but the resulting equiv relation is not transitive equiv(1.0, NAN) && equiv(NAN, 2.0) but not equiv(1.0, 2.0).  
  
I have interpreted this to mean don't pass NANs to sort/max/min/etc. If UBSAN doesn't complain I wouldn't put too much extra thought into it. If it does complain I would use something like `std::numeric_limits<Real>::infinity()` which we know will also retain it's place at the end of the array and then replace infinity with NAN after sorting.

> Username: NAThompson  
> Created_at: 2021-10-27 03:54:11 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r737085978  

> If UBSAN doesn't complain I wouldn't put too much extra thought into it.  
  
From a code legibility standpoint, the fix is pretty awkward. I think I'd rather wait for a bug report in the wild!

---

📁 include/boost/math/tools/cubic_roots.hpp

```diff
  17 |+ }
  18 |+ // Solves ax³ + bx² + cx + d = 0.
  19 |+ // Only returns the real roots, as types get weird for real coefficients and complex roots.
```

> Username: mborland  
> Created_at: 2021-10-24 13:57:29 UTC  
> Updated_at: 2021-10-24 13:59:24 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735122961  

Do you intend to implement for other than real roots in the future? If so it might be worth returning a tuple here because those implementations will require it. If not disregard.

> Username: NAThompson  
> Created_at: 2021-10-24 15:56:40 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735137615  

I don't intend to return the complex roots in the future.  
  
First, if you return the complex roots, then restricting to real coefficients is awkward. But if you have complex coefficients then this algorithm doesn't work.  
  
Second, you'd need to return (say) a `std::array<std::variant<Real, std::complex<Real>>, 3>`, or just always return complex roots, even if the real part is zero.  
  
Finally, I've never seen a use case for the complex roots; my own application was rendering cubic surfaces, which specifically requires sorted real roots.


---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-10-24 17:34:46 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-950363539  

@jzmaddock : I can't build the docs anymore; does the CI build the docs? I'm a bit worried about the unicode in the `.qbk` file.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-10-25 17:33:26 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-951148759  

@NAThompson : I've just killed all the CI except the most recent, that batch of pushes had created a huge backlog...

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-25 17:44:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/703#pullrequestreview-788431264  

📁 include/boost/math/tools/cubic_roots.hpp

```diff
  12 |+ 
  13 |+ namespace detail {
  14 |+     template <typename Real> int sgn(Real val) {
```

> Username: jzmaddock  
> Created_at: 2021-10-25 17:44:16 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r735825814  

Any reason not to use `sign(x)` here instead: https://www.boost.org/doc/libs/1_77_0/libs/math/doc/html/math_toolkit/sign_functions.html ?

> Username: NAThompson  
> Created_at: 2021-10-26 03:20:02 UTC  
> Url: https://github.com/boostorg/math/pull/703#discussion_r736112181  

Yup, fixed.


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-10-25 17:46:59 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-951158800  

> I can't build the docs anymore; does the CI build the docs? I'm a bit worried about the unicode in the .qbk file.  
  
Yes, the CircleCI job builds the docs.  
  
However, a mark one eyeball over the generated docs is still a good idea.  How come you can't build the docs?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2021-10-26 03:10:10 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-951517589  

> @NAThompson : I've just killed all the CI except the most recent, that batch of pushes had created a huge backlog...  
  
Bah, my bad. I still contend the default should be to kill the jobs if you force push an update!  
  
(It's the universe that's wrong, not me.)

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-10-26 03:18:56 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-951521785  

> How come you can't build the docs.  
  
```  
$ ../../b2 doc  
In file included from ../../boost/math/policies/policy.hpp:11:  
../../boost/math/tools/mp.hpp:22:18: warning: variadic templates are a C++11 extension [-Wc++11-extensions]  
template<typename... T>  
```  
  
I can't for the life of me convince `b2` that it's allowed to use C++11.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2021-10-27 03:53:25 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-952514356  

The failing checks are the known issues in the Bernoulli numbers.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-10-28 11:55:39 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-953773827  

>The failing checks are the known issues in the Bernoulli numbers.  
  
What known issues?  
  
It's just annoying to have failures in CI as it makes it so much harder to evaluate PR's.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2021-10-28 17:27:54 UTC  
> Url: https://github.com/boostorg/math/pull/703#issuecomment-954051864  

@jzmaddock : I grepped through the log and found this:  
  
  
```  
../../../boost/math/special_functions/bernoulli.hpp:74:24: note: in instantiation of function template specialization 'boost::math::bernoulli_b2n<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50, boost::multiprecision::backends::digit_base_10, void, int, 0, 0>, boost::multiprecision::et_off>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >' requested here  
1305  
   return boost::math::bernoulli_b2n<T>(i, policies::policy<>());  
1306  
                       ^  
1307  
bernoulli_no_atomic_fail.cpp:14:71: note: in instantiation of function template specialization 'boost::math::bernoulli_b2n<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50, boost::multiprecision::backends::digit_base_10, void, int, 0, 0>, boost::multiprecision::et_off> >' requested here  
1308  
   check_result<boost::multiprecision::cpp_bin_float_50>(boost::math::bernoulli_b2n<boost::multiprecision::cpp_bin_float_50>(4));  
1309  
                                                                      ^  
1310  
1 error generated.  
```  
  
And . . . its an expected error. So my bad; I need to submit a fixup commit.

---
