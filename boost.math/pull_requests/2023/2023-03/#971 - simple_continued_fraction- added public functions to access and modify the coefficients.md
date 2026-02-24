# #971 simple_continued_fraction: added public functions to access and modify the coefficients [Open]

> Username: Tomaqa  
> Created at: 2023-03-29 15:13:33 UTC  
> Updated at: 2023-04-12 13:00:12 UTC  
> Url: https://github.com/boostorg/math/pull/971  

- All coefficients are accessible *and mutable*, and the class can be used as a container.  
- I removed `partial_denominators`, because IMO the name is misleading - front element represents integer part, and is not denominator. It may confuse the user that the function returns sth. where the integer part is omitted.  
- I removed member variable `x_` because I consider it useless - it is only necessary in the constructor, and also it would be difficult to make it consistent with the new mutable member function.  
- The only exception are lines 154-155 (in the previous file), which I do not understand, e.g. how it uses `.x_.backend()`. But if the precision is somehow needed, only precision should be stored, not the original value.  
  
Partially solves #970, but I did not handle documentation.  
  
I also implemented conversion (non-member) function to `boost::rational`, but I am not sure where to place it, because it needs to include both `simple_continued_fraction.hpp` and `rational.hpp`.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-03-29 19:31:18 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1489185446  

> All coefficients are accessible and mutable, and the class can be used as a container.  
  
Why make the coefficients mutable?

---

## Comment 2

> Username: Tomaqa  
> Created_at: 2023-03-29 22:34:16 UTC  
> Updated_at: 2023-03-29 22:43:57 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1489424280  

Mutability simply allows further operations with continued fractions, not only constructing them from floats.  
For example, [rational approximation](https://en.wikipedia.org/wiki/Continued_fraction#Best_rational_approximations) requires to perform binary operations on already built continued fractions, _not with floats_.  
Only const accessors to the coefficients are not enough in cases when a continued fraction is supposed to be a result of a binary operation of two continued fractions.  
I believe that there are many more use cases than the mentioned approximation.  
  
Modifying particular coefficients should not turn the object into an inconsistent state.  
However, it is true that one can set a coefficient a_i, i > 0, to a _non-positive_ number, which would result in an invalid simple continued fraction.  
Also, I realized that also this thing should be taken care of, to keep the objects in the chosen "canonical form":  
```  
// Deal with non-uniqueness of continued fractions: [a0; a1, ..., an, 1] = a0; a1, ..., an + 1].  
// The shorter representation is considered the canonical representation  
```  
Consistency can be easily preserved within `push_back`, but mutable `operator []` may be replaced by a setter.  
  
If these comments are OK with you, I can update the pull request ...

---

## Comment 3

> Username: Tomaqa  
> Created_at: 2023-03-29 22:49:58 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1489438305  

Also, I can update the code in order to fix some of the tests that failed. But not all of them, for example in cases where `partial_denominators` are missing, which I argued that should be removed or renamed.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2023-03-30 00:33:30 UTC  
> Updated_at: 2023-03-30 00:34:00 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1489520670  

> I removed partial_denominators, because IMO the name is misleading - front element represents integer part, and is not denominator. It may confuse the user that the function returns sth. where the integer part is omitted.  
  
The current usage is consistent with [Mathworld's definition](https://mathworld.wolfram.com/SimpleContinuedFraction.html). Are there more authoritative sources which exclude the integer part from the definition?  
  
> Only const accessors to the coefficients are not enough in cases when a continued fraction is supposed to be a result of a binary operation of two continued fractions.  
  
The goal of this class is to convert a floating point number into its best continued fraction approximation. Therefore, unless we change the purpose of the class, it does not make sense to allow it to be mutable.

---

## Comment 5

> Username: Tomaqa  
> Created_at: 2023-03-30 15:40:42 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1490523898  

> The current usage is consistent with [Mathworld's definition](https://mathworld.wolfram.com/SimpleContinuedFraction.html). Are there more authoritative sources which exclude the integer part from the definition?  
  
I only found sources where either "Mathworld's definition" is used, or the coefficients b_i have no specific name.  
So in the end, I suppose that the current form can be kept, I just added a note with clarification that it also includes the integer part.  
Note that [the Mathworld's definition](https://mathworld.wolfram.com/SimpleContinuedFraction.html) contains several mistakes. If it is referenced somewhere in documentation, I would prefer [this Mathworld's definition](https://mathworld.wolfram.com/ContinuedFraction.html) of continued fractions in general, where simple cf. are used as a special case, and the formula and the definition does not contain mistakes ...  
  
> The goal of this class is to convert a floating point number into its best continued fraction approximation. Therefore, unless we change the purpose of the class, it does not make sense to allow it to be mutable.  
  
I believe that this is enough for many use cases, but definitely not for all use cases, and it would be pitty if one would have to implement brand new class for this, where construction from floats is supposed to be necessary too.  
  
Thus, I _updated the merge request_ s.t. it is quite _conservative_, with quite minimal modifications.  
Most importantly, I added non-const `partial_denominators` getter, but as a **`protected`** member. This at least allows the user to use it as a base class and handle non-const operations at his/her own risk.  
  
There is still one issue though. Since both const and non-const `partial_denominators` getters have the same name, the non-const getter is preferred by the compiler in any context where the instance of the class is non-const - which is quite common (one does not always use the instances as const variables). This results in calling the protected getter - compiler error. The user has to always use only const variables or cast the variable to be const (like with `std::as_const`), but I suppose this is incovenient. (I also think this will result in failing the CI tests.)  
Thus, I would suggest to rename the non-const getter. But I am not familiar with Boost coding styles, so I do not know which name to choose...  
  
I also added `int_type` type alias, consistently to `boost::rational`.  
And I added defaulted default, copy and move constructors (I suppose that requiring std=c++11 is OK?)

---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-03-30 17:46:29 UTC  
> Updated_at: 2023-03-30 17:49:44 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1490690685  

> I only found sources where either "Mathworld's definition" is used, or the coefficients b_i have no specific name.  
So in the end, I suppose that the current form can be kept, I just added a note with clarification that it also includes the integer part.  
  
IIRC, I was reading [Khinchin's](https://www.amazon.com/Continued-Fractions-Dover-Books-Mathematics/dp/0486696308) book when I wrote this class. I'll take a look at it and see whether I messed up and used a different definition. In either case, the documentation can be made more clear.  
  
> And I added defaulted default, copy and move constructors (I suppose that requiring std=c++11 is OK?)  
  
Is this necessary? They should've been created by the compiler-or better explicitly deleted.  
  
> Most importantly, I added non-const partial_denominators getter, but as a protected member. This at least allows the user to use it as a base class and handle non-const operations at his/her own risk.  
  
I confess I do not see a good reason to allow non-const modifications.

---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2023-03-30 18:22:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/971#pullrequestreview-1365687236  

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
 158 |-    }
 159 |-    
 170 |+    static_assert(p != 2147483647, "numeric_limits<Real>::max_digits10 == 2147483647");
```

> Username: NAThompson  
> Created_at: 2023-03-30 18:22:45 UTC  
> Updated_at: 2023-03-30 18:22:46 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1153630201  

This is a workaround for variable precision MPFR types.

> Username: Tomaqa  
> Created_at: 2023-03-31 13:36:44 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154487079  

To resolve this, I added member variable `precision_` and set it within the constructor.  
There should be some check like `if constexpr (requires { orig_x.backend(); } ...`, but this is C++20 feature and in C++17 it is quite painful to achieve this ...


---

## Review 8 [Commented]

> Username: mborland  
> Created_at: 2023-03-31 12:46:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/971#pullrequestreview-1366855470  

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
  31 | 
  32 |- template<typename Real, typename Z = int64_t>
  32 |+ template<typename Real, typename I = int64_t>
```

> Username: mborland  
> Created_at: 2023-03-31 12:34:14 UTC  
> Updated_at: 2023-03-31 12:46:05 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154421435  

The set of all integers is represented with Z: https://en.wikipedia.org/wiki/Integer. You'll see it throughout the code base.

> Username: Tomaqa  
> Created_at: 2023-03-31 12:52:58 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154439772  

OK, my motivation was to use the same identifier as is used in `boost::rational`, but OK, I will stick to the `Z`.

---

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
  39 |+     simple_continued_fraction& operator =(const simple_continued_fraction&) = default;
  40 |+     simple_continued_fraction(simple_continued_fraction&&) = default;
  41 |+     simple_continued_fraction& operator =(simple_continued_fraction&&) = default;
```

> Username: mborland  
> Created_at: 2023-03-31 12:36:14 UTC  
> Updated_at: 2023-03-31 12:46:05 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154423359  

The core guidelines recommends against all of this as the compiler will generate them automatically. See: https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-zero

> Username: Tomaqa  
> Created_at: 2023-03-31 12:50:16 UTC  
> Updated_at: 2023-03-31 12:50:17 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154437089  

You are right. I updated the commit a few moments before your review, so I suppose it is already resolved?

---

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
  92 |+ 
  93 |+        const int size_ = b_.size();
  94 |+        for (int i = 1; i < size_; ++i) {
```

> Username: mborland  
> Created_at: 2023-03-31 12:39:23 UTC  
> Updated_at: 2023-03-31 12:46:05 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154426423  

This will result in a warning for `-Wsign-conversion`.

> Username: mborland  
> Created_at: 2023-03-31 13:03:28 UTC  
> Updated_at: 2023-03-31 13:03:48 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1154450818  

`std::vector<T>::size_type` is an unsigned integer so you would need to cast to `const int size_`. Signed overflow is UB while unsigned overflow is defined. I think this has been the case since C99.


---

## Comment 9

> Username: Tomaqa  
> Created_at: 2023-03-31 13:44:31 UTC  
> Updated_at: 2023-03-31 13:45:34 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1491949106  

> I confess I do not see a good reason to allow non-const modifications.  
  
Consider the following example, where I want to compute [best rational approximation within an interval](https://en.wikipedia.org/wiki/Continued_fraction#Best_rational_approximations):  
```c++  
using boost::math::tools::simple_continued_fraction;  
  
const RealT lhs = ...;  
const RealT rhs = ...;  
  
const auto cont_frac1 = simple_continued_fraction(lhs);  
const auto cont_frac2 = simple_continued_fraction(rhs);  
auto& coefs1 = cont_frac1.partial_denominators();  
auto& coefs2 = cont_frac2.partial_denominators();  
  
const size_t max_size = min(coefs1.size(), coefs2.size());  
simple_continued_fraction cont_frac;  
auto& coefs = cont_frac.partial_denominators();  
coefs.reserve(max_size);  
for (size_t i = 0; i < max_size; ++i) {  
    const auto c1 = coefs1[i];  
    const auto c2 = coefs2[i];  
    if (c1 == c2) {  
        coefs.push_back(c1);  
        continue;  
    }  
  
    coefs.push_back(min(c1, c2) + 1);  
    break;  
}  
  
return cont_frac;  
```  
  
It is not possible to construct such `simple_continued_fraction` with only const `partial_denominators`.

---

## Review 10 [Commented]

> Username: mborland  
> Created_at: 2023-04-12 10:52:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/971#pullrequestreview-1381039702  

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
  39 |+     typedef Z int_type;
  40 |+ 
  41 |+     simple_continued_fraction(std::vector<Z> data) : b_{std::move(data)} {
```

> Username: mborland  
> Created_at: 2023-04-12 10:47:04 UTC  
> Updated_at: 2023-04-12 10:52:45 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1163958056  

```diff  
-    simple_continued_fraction(std::vector<Z> data) : b_{std::move(data)} {  
+    simple_continued_fraction(std::vector<Z>&& data) : b_{data} {  
```  
  
As written this makes a copy of the data vector and then moves it into `b_`. I expect you want to take an r-value reference instead.

> Username: Tomaqa  
> Created_at: 2023-04-12 12:54:25 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1164094853  

`simple_continued_fraction(std::vector<Z>)` can be used both with `std::vector<Z>&&` and `const std::vector<Z>&` arguments, whereas `simple_continued_fraction(std::vector<Z>&&)` cannot be used with `const std::vector<Z>&`. In the case of `std::vector<Z>&&` argument, there should no significant difference (`std::vector<Z>` can require one more call to move constructor, which is negligible).  
  
More importantly, I'm pretty sure that your suggestion `simple_continued_fraction(std::vector<Z>&& data) : b_{data}` will call copy constructor on `data`, because you pass it as an lvalue, not as an rvalue.

---

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
 218 |+ // Can be used with `boost::rational` from <boost/rational.hpp>
 219 |+ template <typename Rational, typename Real, typename Z = std::int64_t>
 220 |+ inline Rational to_rational(const simple_continued_fraction<Real, Z>& scf)
```

> Username: mborland  
> Created_at: 2023-04-12 10:52:24 UTC  
> Updated_at: 2023-04-12 10:52:45 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1163963452  

Does this work with other rational types [(e.g. `mpq_t`)](https://gmplib.org/manual/Rational-Number-Functions)?

> Username: Tomaqa  
> Created_at: 2023-04-12 12:57:51 UTC  
> Updated_at: 2023-04-12 12:57:52 UTC  
> Url: https://github.com/boostorg/math/pull/971#discussion_r1164099421  

I assume that `mpq_t` cannot be used since it is C-style struct which requires to call `mpq_init` etc.  
The function can be used with any type `T` that supports `typename T::int_type`, `T(typename T::int_type)` (just integer part) and `T(typename T::int_type, typename T::int_type)` (numerator and denominator).  
I assume C++20 is not supported so I do not use concepts or such.


---

## Comment 11

> Username: mborland  
> Created_at: 2023-04-12 10:54:50 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1505068634  

Is this being superseded by #975?

---

## Comment 12

> Username: Tomaqa  
> Created_at: 2023-04-12 13:00:12 UTC  
> Url: https://github.com/boostorg/math/pull/971#issuecomment-1505234678  

> Is this being superseded by https://github.com/boostorg/math/pull/975?  
  
Totally, I did not know that it will connect back with this PR, so I assume #975 is redundant.

---
