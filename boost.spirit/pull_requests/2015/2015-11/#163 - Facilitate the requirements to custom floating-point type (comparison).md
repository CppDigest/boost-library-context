# #163 Facilitate the requirements to custom floating-point type (comparison) [Closed]

> Username: tomilov  
> Created at: 2015-11-03 16:43:19 UTC  
> Updated at: 2020-05-19 13:39:59 UTC  
> Closed at: 2020-05-19 13:39:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/163  

The change allows the user to make custom floating-point type equality comparable only. operator == (double) is not required now.

---

## Comment 1

> Username: tomilov  
> Created_at: 2015-11-03 16:59:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153417618  

Documentation states, that `T` should be constructible from `int` only (`T(int)`). Whether it should be possible in explicit or implicit way is unspecified, but provided expression imply, that `T` should be explicitly constructible from `int`.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2015-11-03 17:18:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153422931  

I don't see where the X3 documentation lists the requirements for `T`. The v2 documentation also states this:  
  
| boost::spirit::traits::is_equal_to_one(n) | Return true if n is equal to 1.0. Default implementation is provided for float, double and long double. |  
| --- | --- |  
  
which would have to be updated to reflect the changes you have just made since its testing for `1`, and not `1.0`. And `T` would still need a `operator==` for itself in the default implementation of `is_equal_to_one`, which isn't listed in the documentation.

---

## Comment 3

> Username: tomilov  
> Created_at: 2015-11-03 17:31:26 UTC  
> Updated_at: 2015-11-03 17:40:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153426209  

I stick on [Spirit.V2 documentation](http://www.boost.org/libs/spirit/doc/html/spirit/qi/reference/numeric/real.html#spirit.qi.reference.numeric.real.minimum_expression_requirements_for__code__phrase_role__identifier__t__phrase___code_), and concentrate attention to following line of the table of `T`'s requirements:  
  
| **Expression** | **Semantics** |  
| --- | --- |  
| `T(0)` | Construct from an int. |  
  
Why do we need `T::operator == (T const &)` and `T::operator == (double)` simultaneously? Entities must not be multiplied beyond necessity.

---

## Comment 4

> Username: djowel  
> Created_at: 2015-11-03 22:17:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153506880  

I think this makes sense. Unless there are more objections, I'm willing to merge this. The documentation should be augmented with this info. And, better yet, we need tests for such custom floating-point types.

---

## Comment 5

> Username: tomilov  
> Created_at: 2015-11-04 12:39:57 UTC  
> Updated_at: 2015-11-04 12:59:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153708812  

If you decide to start writing tests from a scratch, then look at [this](https://github.com/tomilov/insituc/blob/master/include/floating_point_type_wrapper.hpp#L145).

---

## Comment 6

> Username: djowel  
> Created_at: 2015-11-04 21:47:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-153876639  

What's your interest with this, BTW? Are you intending to use custom numerics with Spirit?

---

## Comment 7

> Username: tomilov  
> Created_at: 2015-11-05 10:10:31 UTC  
> Updated_at: 2015-11-05 10:11:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-154015335  

I intend to provide an example of using a wrapper of CGAL's algebraic numbers or mpfr/gmp for [my JIT-compiler project](https://github.com/tomilov/insituc) (for AST, AST transformations and interpreter parts).

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-10-26 00:41:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/163#issuecomment-433250102  

Closed and opened to kick the CI.

---
