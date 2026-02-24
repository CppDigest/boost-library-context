# #41 Polynomial fixes: for mixed-type arithmetic etc. [Open]

> Username: kundor  
> Created at: 2016-05-15 03:17:53 UTC  
> Updated at: 2022-10-29 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/41  

I was fixing some minor problems in the polynomial documentation, when I noticed that `operator / (polynomial<T>, U)` and `operator % (polynomial<T>, U)` were not actually templated on the type of the scalar `U`, as reported in the docs, but were provided by Boost.Operators and only accepted scalars of type `T`.  
  
This isn't very noticeable, since either way, the code compiles only if `U` is implicitly convertible to `T`. However, it does cause different behavior. For instance, `polynomial<int>{2,4,6} / 1.5` is an identity operation (the `1.5` is truncated to `1`), while `polynomial<int>{2,4,6} * 0.6667` yields {1,2,4}.  
More seriously, if `a` is a `polynomial<int>`, `a = a / 1.5` and `a /= 1.5` would give different results.  
  
To fix this, I added templated implementations of these operators just like all the other polynomial-scalar operators.  
  
For consistency, I used Boost.Operators to provide all the polynomial-polynomial operations (it was being used just for division, modulus, and operator !=), which are conveniently packed together in the class `ordered_euclidean_ring_operators< polynomial<T> >`. (This entailed also adding an `operator <`, which also enables polynomials to be put in `std::set` etc. It compares by degree first, so that it's compatible with the Euclidean ordering, then lexicographically starting with the highest-degree coefficient.)  
  
The self-assign operators are all templated on `polynomial<U>`. However, `operator /=` and `%=` call out to `quotient_remainder`, which only takes two matching `polynomial<T>` arguments. So it's ambiguous what to convert when calling `quotient_remainder(polynomial<T>, polynomial<U>)`, causing an error.  
  
To fix this, `polynomial<T>::operator /=(const polynomial<U>& q)` can call `quotient_remainder(*this, polynomial<T>(q))`.  But then you might as well not template the operator on type U, and just make it `polynomial::operator /=(const polynomial& q)`; you can still use it with any `polynomial<U>` because there is an implicit conversion to `polynomial<T>`.  
  
Unfortunately, just removing the templating causes `polynomial<U>` arguments to choose the scalar version `operator /=(const U& value)` instead. So I had to guard that function with `enable_if< boost::is_convertible<U,T> >`.  
  
This situation _does_ result in inconsistent behavior. For instance, multiplying a `polynomial<int>` by a `polynomial<double>` will do each multiplication with `double`s before truncating back to `int`, whereas dividing by a `polynomial<double>` will first truncate all the values to `int`.  
Similarly, with `polynomial<int> a{3}`, `a /=  1.5` results in 2, but `a /= polynomial<double>{1.5}` results in 3.  
  
Changing this, so that it promotes internal computations and then truncates, would mean changing the implementation of quotient_remainder.  
  
Finally, when `T` is integral, there can be a remainder after dividing by a scalar, so I added an implementation of `operator %=` for this (instead of just setting to zero as before.) It uses `enable_if_c<std::numeric_limits<T>::is_integer>`—in theory, the algorithm used should work for other types, but with floating point types it would probably end up with annoying near-zero coefficients.   
  
I would be happy to fix up this PR if you want to merge #39 first (it will conflict a little.)

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-05-15 11:25:37 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219279691  

Thanks for picking up these defects. I remember now when I was implementing `operator >>`, etc that it did not work using the Boost.Operator facility because I needed a new template type for the right-hand parameter.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2016-05-15 11:39:16 UTC  
> Updated_at: 2016-05-15 11:50:52 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219280297  

It would be great if you could include in this PR the unit tests that would otherwise fail without these changes , including explicit tests of the new `modulus` function.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-05-15 11:53:21 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219281253  

Hmmm, I guess this begs the question as to whether non-PoD numbers (such as polynomial) should promote according to the same rules as PoDs. What do the multiprecision classes do, John? How do they handle arithmetic with a mixture of types?

---

## Comment 4

> Username: kundor  
> Created_at: 2016-05-15 15:46:09 UTC  
> Updated_at: 2016-05-15 18:03:08 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219293220  

Speaking of which, is there really a need to template the shift operators on the right-hand parameter? Is there a case for calling it with anything but an `int` (or something convertible thereto)? I don't think this class is meant to support polynomials of degree 32,000 or more ;-) (a signed int goes up at least to 2^15-1, by the standard.) (Taking an `int` and checking that it's positive is preferable to taking an unsigned number, in that otherwise a call `p <<= -1` will compile without complaint, and silently try to extend `p` by 4 billion elements.)

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-05-15 15:54:14 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219293770  

On 15/05/2016 16:46, Nick Matteo wrote:  
  
> Speaking of which, is there really a need to template the shift   
> operators on the right-hand parameter? Is there a case for calling it   
> with anything but an |int| (or something convertible thereto)? I don't   
> think this class is meant to support polynomials of degree 32,000 or   
> more ;-) (a signed int goes up at least to 2^15-1, by the standard.)   
> Taking an |int| and checking that it's positive is preferable to   
> taking an unsigned number, in that otherwise a call |p <<= -1| will   
> compile without complaint, and silently try to extend |p| by 4 million   
> elements.  
  
I wouldn't think so no.  Jeremy?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-05-15 16:03:57 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219294316  

On 15/05/2016 12:53, Jeremy W. Murphy wrote:  
  
> Hmmm, I guess this begs the question as to whether non-PoD numbers   
> (such as polynomial) should promote according to the same rules as   
> PoDs. What do the multiprecision classes do, John? How do they handle   
> arithmetic with a mixture of types?  
  
Well first off they don't use Boost.Operators which is rather ill suited   
to this kind of thing, but basically given:  
  
a op b  
  
If there is an implicit (ie non-lossy) conversion from b to a, then b is   
converted to typeof(a), otherwise if a is implicitly convertible to   
typeof(b) then a gets promoted.  
  
Of course internally, the value may be used directly where it is more   
efficient to do so, but the operators themselves enforce the above rule.  
  
In this situation, for:  
  
polynomial<T> op U  
  
we would want to enforce that there is an implicit, non-lossy conversion   
from U to T, which means no multiplication of polynomial<int> by a   
double etc.  
  
The logic used is somewhat multiprecision-specific, and in   
boost/multiprecision/traits/is_restricted_conversion.hpp  
  
HTH, John.

---

## Comment 7

> Username: kundor  
> Created_at: 2016-05-15 17:47:47 UTC  
> Updated_at: 2016-05-15 17:51:24 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-219300001  

I rebased the branch, and added the tests Jeremy asked for.  
  
Is it necessary for tests to be pre-C++11? It's kind of painful.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2016-05-19 13:29:12 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-220324003  

With respect to the shift operators, no, there is no particular need for them to be templated, and off the cuff it only makes sense to call them with an integral type. How to handle negative values raises a curious thought, though: should `operator>>(-x)` be equivalent to `operator<<(x)`? I understand that for integers, shifting by a negative amount is undefined behaviour, so probably we should follow suit, but we're not obliged to.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-05-19 13:39:18 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-220326803  

Hmmm, so for `operator*=(U)` we should be restricting `U` to be implicitly convertible to `T`?

---

## Comment 10

> Username: kundor  
> Created_at: 2016-05-19 14:31:39 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-220342229  

Re: `operator*=(U)`.  We can restrict all the scalar operations to arguments that are implicitly convertible to `T`, by just removing the templating and only taking `T`. The question is whether we want  
  
``` C++  
polynomial<int> p{3,2};  
p *= 1.5;  
```  
  
to give us 3x + 4, analogous to what `int i=3; i *= 1.5;` would do,  or be an identity operation,  or be a compile-time error.  
  
Personally, I would rather have it act like built-in `int`s, i.e. multiply by the double and then truncate, which is what it does now.  
  
Making it an error would require a templated version of the function just to fail, using something like the `is_lossy_conversion` trait from multiprecision, which seems like overkill.  
  
On the other hand, removing the templating from poly-poly operations (`operator *=(polynomial<U>)` etc.), would make their behavior more consistent (in that currently, `operator/=(polynomial)` must act that way.) I think currently I am in favor of removing arithmetic between polynomial<T> and polynomial<U> entirely, which means removing the template from each `operator op=(polynomial)` function, and making the conversion constructor `polynomial(const polynomial<U>& p)` `explicit`.

---

## Comment 11

> Username: kundor  
> Created_at: 2016-05-19 15:07:14 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-220353538  

Commit 910a62a removes arithmetic between polynomials with different base types, so you'll have to explicitly cast one operand or the other (e.g. if `p` is a `polynomial<int>` and `q` is a `polynomial<double>`, you can write `polynomial<double>(p) * q`, or `p *= polynomial<int>(q)`.  
  
All the tests in test_polynomial.cpp still pass.

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2016-05-22 23:06:33 UTC  
> Updated_at: 2016-05-28 20:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/41#discussion_r64157967  

Should `modulus` still have a `U` template?

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2016-05-22 23:11:14 UTC  
> Updated_at: 2016-05-28 20:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/41#discussion_r64158026  

Since this is a compile-time constant, can't we combine it with `enable_if` for static dispatch?

---

## Comment 14

> Username: kundor  
> Created_at: 2016-05-22 23:27:01 UTC  
> Updated_at: 2016-05-28 20:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/41#discussion_r64158307  

Yes, `operator %=(U)`, and the other self-modifying operators for scalar arguments, are all still templated. We can change that if you want to be more strict.

---

## Comment 15

> Username: kundor  
> Created_at: 2016-05-23 00:27:10 UTC  
> Updated_at: 2016-05-28 20:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/41#discussion_r64159573  

I seem to recall that using `enable_if` based only on `T`, which is not one of the template parameters for the function, doesn't work. Let me check.

---

## Comment 16

> Username: kundor  
> Created_at: 2016-05-23 01:22:14 UTC  
> Updated_at: 2016-05-28 20:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/41#discussion_r64160956  

Yes, I was right: GCC 6.1, GCC 5.3, and Clang 703.0.31 all complain vociferously. To separate them, it's necessary to add a dummy condition to the enable_if that depends on `U`, e.g. go back to `BOOST_DEDUCED_TYPENAME enable_if_c<boost::is_convertible<U,T>::value &&  
std::numeric_limits<T>::is_integer, polynomial >::type& operator %=(const U& value)`.  
  
I don't think this mess is worth it:  
- the modulus operator on a polynomial doesn't seem performance-critical  
- a conditional testing a compile-time boolean should only emit the chosen branch anyway  
  
You can observe that the last thing is true [at godbolt](https://godbolt.org/g/h4qmrG).

---

## Comment 17

> Username: jeremy-murphy  
> Created_at: 2016-06-05 02:56:07 UTC  
> Updated_at: 2016-06-05 05:37:44 UTC  
> Url: https://github.com/boostorg/math/pull/41#issuecomment-223790210  

I probably agree with all of the changes in this PR in principle, but to be honest, I find it too big to think about all at once and be sure that every detail is right. I suggest that you split this into three PRs, one for each of the independent things being changed:  
1. the bug fix for `*` versus `*=` (which is the most important change)  
2. adding `operator<` leading to `ordered_euclidean_ring_operators`  
3. changes to the shift operators.  
  
I realize that's not fun but that's what I would do.

---
