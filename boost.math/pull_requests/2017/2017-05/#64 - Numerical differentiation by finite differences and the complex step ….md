# #64 Numerical differentiation by finite differences and the complex step … [Merged]

> Username: NAThompson  
> Created at: 2017-05-03 00:29:56 UTC  
> Updated at: 2018-02-14 19:45:52 UTC  
> Merged at: 2018-02-13 19:47:55 UTC  
> Closed at: 2018-02-13 19:47:55 UTC  
> Url: https://github.com/boostorg/math/pull/64  

…derivative.  
  
This is ready for review.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2017-05-03 20:33:43 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-299027023  

> …derivative.> This is ready for review.  
It seems like a good piece of work. Thank you.  
  
One tricky issue is in "finite_difference_derivative()".The template parameter "order" is provided as acompile-time constant. Then it is used in run-timedecisions in if-clauses. Some compilers at high warninglevels issue warning for non-necessary comparison.  
This is really a minor issue, rather a detail.  
  
A bunch of strenuous enable_if could help. But this  
might just obfuscate the code.  
Nice. Keep going...  
Cheers, Chris  
  
  
  
   
  
    On Wednesday, May 3, 2017 2:30 AM, Nick <notifications@github.com> wrote:  
   
  
 …derivative.This is ready for review.  
You can view, comment on, or merge this pull request online at:  
  https://github.com/boostorg/math/pull/64  
Commit Summary  
     
   - Numerical differentiation by finite differences and the complex step derivative.  
  
File Changes  
     
   -  M doc/math.qbk (2)   
   -  A doc/sf/numerical_differentiation.qbk (99)   
   -  A include/boost/math/tools/numerical_differentiation.hpp (234)   
   -  M test/Jamfile.v2 (1)   
   -  A test/test_numerical_differentiation.cpp (252)   
  
Patch Links:  
     
   - https://github.com/boostorg/math/pull/64.patch  
   - https://github.com/boostorg/math/pull/64.diff  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-05-03 21:18:47 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-299038544  

Thanks Chris!  
  
I think the right way to do it is the C++17 construct `if constexpr (order == 6) ...`. I can compile this syntax with clang 4.0, but I suppose it'll be 3-4 years before boost allows C++17 into the trunk. Another perfectly sensible choice is just to not use the order as a template parameter. The cost shouldn't be too high, though it's nice to get the `static_assert`.  
  
I read about `std::enable_if`, but I couldn't see a clear way to make the syntax work; the only examples I found were for conditional compilation of member functions . . .

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2017-05-04 01:07:24 UTC  
> Updated_at: 2017-05-04 01:08:31 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-299076421  

I agree with Chris that `finite_difference_derivative` is awkward as it stands. It's not just about static dispatch on the order, there are also several areas of shared logic that it would be ideal to modularize. From my quick perusal, it appears that the higher order cases effectively do the same work as a lower order case (with some order-specific variation), plus something additional afterwards. So if possible, it would be great to capture the relationship that higher-order functions re-use lower-order functions.  
  
I think `std::enable_if` should be able to do the trick for what Chris suggests; off the top of my head, you can simply test the expression `order == 1`. And although it would add more code to the signature of each function, I think it would be worth it.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-05-04 02:11:35 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-299082605  

It might be possible that the higher-order functions can share code with the lower order functions, but they can't share work, as the stepsize is dependent on the order. Presumably the code could be shared (the most egregious of this is the code used to ensure that `x+h` is representable), but the function evaluations would be difficult. For example, the first order finite-difference does not evaluate `f` on a subset of the stencil of the second order finite-difference. However, the second-order stencil is a subset of the fourth-order, fourth a subset of the sixth, and sixth a subset of the eighth. The question is then whether employing this relation for more transparent code than what currently exists. I'll chew on it and try to find a way to make it nicer, but I'm not sure I won't cause other aesthetic problems in the process.

---

## Review 5 [Commented]

> Username: ckormanyos  
> Created_at: 2018-02-10 21:44:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/64#pullrequestreview-95634281  

Thank you for contributing Nate.  
  
The documentation looks good. As do the tests.  
  
I just took a very quick look and found a few  
very minor potential comments.  
  
In particular, there are a few very pedantic comments  
on "numerical_differentiation.hpp".  
  
The comment on line 32: "The second algorithm is, IMO, miraculous."  
This seems like a strong statement. It might be better to  
use less strong wording such as  
"The second algorithm can be very powerful in certain situations."  
  
In line 59, there is very subtle issue.  
Consider the use of numeric_limits<Real>::max().  
A certain compiler defines the MACROS min() and max().  
And this leads to conflict of symbols with the  
standardized member functions min() and max() in  
numeric_limits. So in boost, we use:  
(numeric_limits<Real>::max)().  
This is not a problem with your code, rather  
a workaround that we need for one class of compiler.  
  
Same on lines 73, 74.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-02-10 22:27:24 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-364700784  

You're right about the language being too strong. In [Corless's book](https://www.amazon.com/Graduate-Introduction-Numerical-Methods-Viewpoint/dp/1493953109), he demonstrates that the ability to compute a function on the complex plane is equivalent to being able to compute the derivative of the real function. This complex-step derivative should be regarded as encapsulating that equivalence in a compact code.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-02-11 17:58:47 UTC  
> Url: https://github.com/boostorg/math/pull/64#issuecomment-364772275  

Moved to https://github.com/boostorg/math/pull/110 for integration testing.  
  
You may think me silly, but I've made some trivial changes so that C++17 is no longer required without loss of compile-time efficiency.

---
