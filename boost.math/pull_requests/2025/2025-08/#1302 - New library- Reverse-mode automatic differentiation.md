# #1302 New library: Reverse-mode automatic differentiation [Merged]

> Username: demroz  
> Created at: 2025-08-16 13:42:18 UTC  
> Updated at: 2025-12-22 13:20:16 UTC  
> Merged at: 2025-08-26 09:38:04 UTC  
> Closed at: 2025-08-26 09:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/1302  

This pull request introduces a new library for reverse-mode automatic differentiation. Its a tape based reverse mode autodiff, so the idea is to build a computational graph, and then call backward on the entire graph to compute all the gradients at once.   
  
Currently it supports all the basic operations (+,-,/,/), everything listed in conceptual requirements for real number types, and boost calls to `erf`, `erc`, `erf_inv` and `erfc_inv`. Everything is tested up to the 4th derivative. The list of tests:  
`test_reverse_mode_autodiff_basic_math_ops.cpp`  
`test_reverse_mode_autodiff_comparison_operators.cpp`  
`test_reverse_mode_autodiff_constructors.cpp`  
`test_reverse_mode_autodiff_error_functions.cpp`  
`test_reverse_mode_autodiff_flat_linear_allocator.cpp`  
`test_reverse_mode_autodiff_stl_support.cpp`  
  
There are also two examples in the example directory:  
`reverse_mode_linear_regression_example.cpp` -> simple linear regression that demonstrates how this library can be used for optimization  
  
`autodiff_reverse_black_scholes.cpp` -> a rewrite of the forward mode equivalent.  
  
Important notes  
1. The intent of the library is to be an engine for gradient based optimization. (In the future I'd be interested in adding some gradient based optimizers to boost if there is interest.) I wrote it with first derivatives in mind. Although everything is tested up to the 4th derivative, the library is intended to be optimally used for calculating first order derivatives. Its best to use forward mode autodiff if very high orders are needed.  
2. The library is based on expression templates. This means that although my autodiff variable rvar does satisfy all the requirements of a real-type, it doesn't necessarily play nicely with boost special functions. For example:  
```cpp  
rvar<double,1> x = make_rvar<T,1>(1.0);  
rvar<double,1> y = make_rvar<T,1>(1.0);  
rvar<double,1> z = make_rvar<T,1>(1.0);  
auto f = x+y*z;  
```  
f in this case is not actually type `rvar`, but `add_expr<rvar,mult_expr<rvar,rvar>>`  
  
3. reverse_mode_autodiff_memory_management.hpp has a flat_linear_allocator class thats essentially a specialized memory arena. It uses placement `new` for memory allocations. This is a deliberate design choice. The flat_linear_allocator destructor explicitly calls the destructors of individual elements. Explicit calls to `delete` shouldn't be needed here  
  
Thank you, and looking forward to your feedback

---

## Comment 1

> Username: demroz  
> Created_at: 2025-08-17 17:25:25 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3194535095  

looks like msvc errors are gone. Not sure why CI fails but looking inside it doesn't seem to be related to any of my code

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-17 18:58:49 UTC  
> Updated_at: 2025-08-17 18:59:28 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3194587667  

> Not sure why CI fails but looking inside it doesn't seem to be related to any of my code  
  
The failure(s) in CI seem harmless, as you mentioned.  
  
The job that failed CI is on Drone. This, in fact, very many jobs running on various older runner images on Drone. Most of the tests for older compilers such as GCC 7, 8 or old clang. And non-x86_64 architectures (insofar as these are available) run on Drone.  
  
But sometimes, Drone has a hard time acquiring a VM or setting it up properly. So your failures failed in about 3 minutes - too short for a normal run, as these are mostly 7 - 30 minutes. It looks like all the Drone failures were the failure to properly setup LINUX. It gets annoying, but Drone has to be manually judged sometimes to see if failures were real or bogus. This gets doubly annoying when there is actually mixture of real and bogus errors. Which is what happens when we write new code. Sigh, that's just how it goes.  
  
On the upside, GHA is super-reliable nowdays.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-08-18 07:55:08 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3195545226  

Hi @demroz I will be taking a dedicated look at this hopefully in the next few days. I intend to extend one of your examples and really get my hands onto this code and learn more about it.  
  
I'll also be looking into top-level quality aspects such as retaining code coverage and compiler warnings (if there are any). I might have questions along the way. Please give me a few days on this.  
  
We could also benefit later from some feedback later from John and Matt.  
  
Let me get into this thing for a few days.

---

## Review 4 [Commented]

> Username: mborland  
> Created_at: 2025-08-18 11:17:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1302#pullrequestreview-3127824201  

A lot of my comments can be repeated in many places (e.g. constants, using statements, etc), but I didn't as to not completely overload this.

📁 example/autodiff_reverse_black_scholes.cpp

```diff
   9 |+ 
  10 |+ template<typename X>
  11 |+ X phi(X const& x)
```

> Username: mborland  
> Created_at: 2025-08-18 10:37:30 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282015026  

I would use something more descriptive like `typename Real` to indicate what people should be using for `x`

> Username: demroz  
> Created_at: 2025-08-18 18:45:36 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283173017  

should be fixed

---

📁 example/autodiff_reverse_black_scholes.cpp

```diff
  11 |+ X phi(X const& x)
  12 |+ {
  13 |+     return one_div_root_two_pi<double>() * exp(-0.5 * x * x);
```

> Username: mborland  
> Created_at: 2025-08-18 10:39:21 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282019161  

You can then do: `one_div_root_two_pi<Real>()` so we're not losing precision on say `Real = long double`

> Username: demroz  
> Created_at: 2025-08-18 18:45:48 UTC  
> Updated_at: 2025-08-18 18:45:49 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283173420  

fixed

---

📁 example/autodiff_reverse_black_scholes.cpp

```diff
  34 |+         return exp(-r * tau) * K * Phi<T>(-d2) - S * Phi<T>(-d1);
  35 |+     default:
  36 |+         throw std::runtime_error("Invalid CP value.");
```

> Username: mborland  
> Created_at: 2025-08-18 10:40:42 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282021928  

You don't need the `std::` since you already are `using namespace std`

> Username: demroz  
> Created_at: 2025-08-18 19:12:41 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283225717  

removed


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_operator_overloads.hpp

```diff
  21 |+    * */
  22 |+     using lhs_type   = LHS;
  23 |+     using rhs_type   = RHS;
```

> Username: mborland  
> Created_at: 2025-08-18 10:42:33 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282025657  

It doesn't look like you ever use these typedefs. I would just name LHS and RHS appropriately so that you don't need to immediately create typedefs

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_operator_overloads.hpp

```diff
  49 |+    * */
  50 |+     using arg_type   = ARG;
  51 |+     using value_type = T;
```

> Username: mborland  
> Created_at: 2025-08-18 10:43:42 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282028404  

Same thing down the line. Name your template types so that you don't immediately make typedefs at the top of each struct

> Username: demroz  
> Created_at: 2025-08-18 18:46:05 UTC  
> Updated_at: 2025-08-18 18:46:06 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283173858  

fixed

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_operator_overloads.hpp

```diff
  54 |+         : abstract_unary_expression<T, order, ARG, add_const_expr<T, order, ARG>>(arg_expr, v){};
  55 |+     inner_t              evaluate() const { return this->arg.evaluate() + inner_t(this->constant); }
  56 |+     static const inner_t derivative(const inner_t &argv, const inner_t &v, const T &constant)
```

> Username: mborland  
> Created_at: 2025-08-18 10:45:32 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282032644  

I think something like:  
  
```  
static const inner_t derivative(const inner_t& /*argv*/, const inner_t& /*v*/, const T& /*constant*/)  
```  
  
Or   
  
```  
static const inner_t derivative(const inner_t&, const inner_t &, const T&)  
```  
  
Is preferable otherwise the users will get a ton of unused parameter warnings.

> Username: demroz  
> Created_at: 2025-08-19 09:24:21 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2284670101  

fixed

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_operator_overloads.hpp

```diff
 159 |+     static const inner_t left_derivative(const inner_t &l, const inner_t &r, const inner_t &v)
 160 |+     {
 161 |+         return 1.0 / r;
```

> Username: mborland  
> Created_at: 2025-08-18 10:47:52 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282039473  

```diff  
-        return 1.0 / r;  
+        return static_cast<inner_t>(1.0) / r;  
```  
  
Anywhere that you have a floating point constant like this you'll want to wrap with a proper cast. Reasoning is some types will throw/warn/error because they are of different precision. E.g. types from `<stdfloat>`

> Username: demroz  
> Created_at: 2025-08-18 18:46:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283174551  

should be fixed now

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_operator_overloads.hpp

```diff
 293 |+ {
 294 |+     /* rvar - float = rvar + (-float) */
 295 |+     return add_const_expr<T, order, ARG>(arg, static_cast<T>(-1.0 * v));
```

> Username: mborland  
> Created_at: 2025-08-18 10:49:34 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282043717  

Why not just -v since it's not an expression template?

> Username: demroz  
> Created_at: 2025-08-18 18:46:46 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283174965  

fixed


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_erf_overloads.hpp

```diff
 141 |+         using multiprecision::exp;
 142 |+         using multiprecision::pow;
 143 |+         using multiprecision::sqrt;
```

> Username: mborland  
> Created_at: 2025-08-18 10:57:58 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282063584  

Do we need to explicitly include these? I believe not

> Username: demroz  
> Created_at: 2025-08-18 18:46:59 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283175260  

removed

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_erf_overloads.hpp

```diff
 144 |+         using std::exp;
 145 |+         using std::pow;
 146 |+         using std::sqrt;
```

> Username: mborland  
> Created_at: 2025-08-18 10:58:23 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282065387  

All of the `using std::` math functions can be replaced with `BOOST_MATH_STD_USING`

> Username: demroz  
> Created_at: 2025-08-18 18:47:10 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283175564  

removed


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_expression_template_base.hpp

```diff
   6 |+ #ifndef REVERSE_MODE_AUTODIFF_EXPRESSION_TEMPLATE_BASE_HPP
   7 |+ #define REVERSE_MODE_AUTODIFF_EXPRESSION_TEMPLATE_BASE_HPP
   8 |+ #include <stddef.h>
```

> Username: mborland  
> Created_at: 2025-08-18 11:01:08 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282072818  

`<cstddef>`


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_memory_management.hpp

```diff
  15 |+ #include <type_traits>
  16 |+ #include <vector>
  17 |+ #include <boost/assert.hpp>
```

> Username: mborland  
> Created_at: 2025-08-18 11:02:31 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282075525  

`<boost/math/tools/assert.hpp>`

> Username: demroz  
> Created_at: 2025-08-18 19:05:23 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283210487  

changed

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_memory_management.hpp

```diff
  83 |+     reference operator*()
  84 |+     {
  85 |+         BOOST_ASSERT(index_ >= begin_ && index_ < end_);
```

> Username: mborland  
> Created_at: 2025-08-18 11:02:46 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282076033  

`BOOST_MATH_ASSERT`

---

📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_memory_management.hpp

```diff
 330 |+             for (size_t i = 0; i < n; ++i) {
 331 |+                 new (ptr + i) T();
 332 |+             }
```

> Username: mborland  
> Created_at: 2025-08-18 11:06:24 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282083542  

I would add a null check with your placement new  
   
T *ptr = &(*data_[bid])[iid];  
if (ptr != nullptr) {  
    new (ptr + i) T();  
} else {  
    // Handle error  
}

> Username: demroz  
> Created_at: 2025-08-18 18:52:41 UTC  
> Updated_at: 2025-08-18 18:54:22 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283185206  

I think the nullpointer check is redundant here  
  
`buffer_ptr `is a unique_ptr  
`make_unique` never returns null  
and i always check or create a buffer before writing to it with   
  
```cpp  
if (item_id() == 0 && total_size_ != 0) allocate_buffer();  
```  
  
so `data_[bid]` is never null  
`&(*data_[bid])[iid]` is then never null  
  
The only thing that could possibly throw here is the constructor for T

> Username: mborland  
> Created_at: 2025-08-19 10:47:48 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2284866571  

You're correct; no change needed then.


📁 include/boost/math/differentiation/autodiff_reverse.hpp

```diff
  30 |+ #include <type_traits>
  31 |+ #include <vector>
  32 |+ #define BOOST_MATH_BUFFER_SIZE 65536
```

> Username: mborland  
> Created_at: 2025-08-18 11:10:17 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282091135  

I would do `static constexpr std::size_t buffer_size = 65536U` inside your namespace

> Username: demroz  
> Created_at: 2025-08-18 19:03:25 UTC  
> Updated_at: 2025-08-18 19:03:26 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283206555  

I like having a macro here so the user can do -DBOOST_MATH_BUFFER_SIZE 32768 and benchmark the library for their hardware if they want to. I admit that I kind of just pulled the 65536 number from thin air, we could always do something like  
  
```cpp  
#ifndef BOOST_MATH_BUFFER_SIZE  
#define BOOST_MATH_BUFFER_SIZE 65536  
#endif  
  
namespace boost::math::differentiation::reverse_mode{  
static constexpr std::size_t buffer_size = BOOST_MATH_BUFFER_SIZE  
}  
```  
  
what do you think?

> Username: mborland  
> Created_at: 2025-08-19 10:45:41 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2284862274  

> I like having a macro here so the user can do -DBOOST_MATH_BUFFER_SIZE 32768 and benchmark the library for their hardware if they want to. I admit that I kind of just pulled the 65536 number from thin air, we could always do something like  
>   
> ```c++  
> #ifndef BOOST_MATH_BUFFER_SIZE  
> #define BOOST_MATH_BUFFER_SIZE 65536  
> #endif  
>   
> namespace boost::math::differentiation::reverse_mode{  
> static constexpr std::size_t buffer_size = BOOST_MATH_BUFFER_SIZE  
> }  
> ```  
>   
> what do you think?  
  
Having the user configuration option makes sense to me.

---

📁 include/boost/math/differentiation/autodiff_reverse.hpp

```diff
   6 |+ #define BOOST_MATH_DIFFERENTIATION_AUTODIFF_HPP
   7 |+ 
   8 |+ #include <boost/cstdfloat.hpp>
```

> Username: mborland  
> Created_at: 2025-08-18 11:10:44 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282092104  

You shouldn't need this header for anything

> Username: demroz  
> Created_at: 2025-08-18 19:03:39 UTC  
> Updated_at: 2025-08-18 19:03:40 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283206970  

removed

---

📁 include/boost/math/differentiation/autodiff_reverse.hpp

```diff
  25 |+ #include <boost/math/tools/config.hpp>
  26 |+ #include <boost/math/tools/promotion.hpp>
  27 |+ #include <cassert>
```

> Username: mborland  
> Created_at: 2025-08-18 11:11:15 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282093185  

Instead of naked `assert` prefer using `BOOST_MATH_ASSERT`

> Username: demroz  
> Created_at: 2025-08-18 19:05:08 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283210025  

changed

---

📁 include/boost/math/differentiation/autodiff_reverse.hpp

```diff
 580 |+         gradient_vector.reserve(x.size());
 581 |+ 
 582 |+         for (auto xi : x) {
```

> Username: mborland  
> Created_at: 2025-08-18 11:14:21 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282099474  

Is it expensive to make copies of each `xi`?

> Username: demroz  
> Created_at: 2025-08-18 19:04:01 UTC  
> Updated_at: 2025-08-18 19:04:02 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283207930  

typo, changed to auto& xi


📁 .gitignore

```diff
  29 | .ninja*
  30 | a.out
  31 |+ include/boost/math/differentiation/build/
```

> Username: mborland  
> Created_at: 2025-08-18 11:16:19 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282103459  

Is anything actually being built?

> Username: demroz  
> Created_at: 2025-08-18 19:04:59 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283209789  

I use cmake/qtcreator. This is legacy from building my tests directly in math/differentation. I removed it


📁 CMakeLists.txt.user

```diff
   1 |+ <?xml version="1.0" encoding="UTF-8"?>
```

> Username: mborland  
> Created_at: 2025-08-18 11:16:41 UTC  
> Updated_at: 2025-08-18 11:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2282104307  

This file should be deleted from the PR

> Username: demroz  
> Created_at: 2025-08-18 19:04:10 UTC  
> Updated_at: 2025-08-18 19:04:11 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2283208295  

removed


---

## Comment 5

> Username: NAThompson  
> Created_at: 2025-08-18 14:33:54 UTC  
> Updated_at: 2025-08-18 14:38:51 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3197193463  

Just as a quick suggestion: The [test_functions_for_optimization.hpp](https://github.com/boostorg/math/blob/develop/test/test_functions_for_optimization.hpp) might be a very nice place to do unit tests for this as most of these are well suited for reverse mode AD; e.g., mapping from ℝ^n →ℝ.  
  
As an aside: The gradient based optimizers are a great idea, and would make a nice addition to the black-box optimizers we currently have.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-08-19 14:28:29 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201002782  

Hi @demroz I'm startig to clone your branch and look at the code. I must admit, I'm not very experienced in the problem domain of optimization.  
  
I'd like to simply calculate a derivative for built-ins and a Multiprecision type. Is there some example or simple code how to just calculate a numerical derivative?

---

## Comment 7

> Username: demroz  
> Created_at: 2025-08-19 14:41:07 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201047175  

Hi @ckormanyos   
  
how about some of the test cases under   
test/test_reverse_mode_autodiff_basic_math_ops.cpp  
test/test_reverse_mode_autodiff_stl_support.cpp  
  
for example in test/test_reverse_mode_autodiff_basic_math_ops.cpp  
line 145 defines a function  
```cpp  
template<typename T>  
T f(T x, T y)  
{  
    return (x * y) / ((x + y) * (x - y));  
}  
```  
below that I computed the first 4 derivatives with sympy  
then the test case on line 278  
```cpp  
BOOST_AUTO_TEST_CASE_TEMPLATE(first_derivative, T, all_float_types)  
{  
    //RandomSample<T> rng{-1, 1};  
    T x = 1.1224;   //rng.next();  
    T y = 5.213414; //rng.next();  
  
    rvar<T, 1>      x_ad              = x;  
    rvar<T, 1>      y_ad              = y;  
  
    //T               fv                = f(x, y);  
    rvar<T, 1>      f_ad              = f(x_ad, y_ad);  
    auto            grad_f_analytical = grad_f_a(x, y);  
    /* intended use case */  
    f_ad.backward(); // <- this computes the derivatives in place  
  
    BOOST_REQUIRE_CLOSE(x_ad.adjoint(), grad_f_analytical[0], boost_close_tol<T>());  
    BOOST_REQUIRE_CLOSE(y_ad.adjoint(), grad_f_analytical[1], boost_close_tol<T>());  
  
    gradient_tape<T, 1, BOOST_MATH_BUFFER_SIZE>& tape = get_active_tape<T, 1>();  
    tape.zero_grad();  
  
    /* grad test */  
    auto grad_func_test_grad    = grad(f_ad, &x_ad, &y_ad); // <- this returns a copy of the gradient vector w.r.t x and t  
    /* grad_nd test */  
    auto grad_nd_func_test_grad = grad_nd<1>(f_ad, &x_ad, &y_ad); // <- same thing, but use this function for higher order derivatives  
  
    for (int i = 0; i < 2; i++) {  
        BOOST_REQUIRE_CLOSE(grad_func_test_grad[i], grad_f_analytical[i], boost_close_tol<T>());  
        BOOST_REQUIRE_CLOSE(grad_nd_func_test_grad[i], grad_f_analytical[i], boost_close_tol<T>());  
    }  
}  
```  
below this test case are the higher order derivative test cases. You can check test/test_reverse_mode_autodiff_stl_support.cpp for more examples. Let me know if anything isn't clear

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-08-19 15:15:31 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201175628  

> how about some of the test cases under ...  
  
Yeah, now we're getting into it. Thx @demroz.  
  
OK I pulled that code out into a standalone (non-boost) test and its working. See below.  
  
I have my first findings:  
  
- The class `rvar` is referred to as both a `class` as well as a `struct`. I think it should be a class everywhere.  
- The boost guys like `TemplateParamsLikeThis`. So when your parameter is `order` you might wand `Order` or a more specific word like `DerivativeOrder`.  
  
I'll work through more and more code and report findings on the go.  
  
```  
#include <boost/math/differentiation/autodiff_reverse.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <sstream>  
#include <vector>  
  
namespace local  
{  
  template<typename NumericType>  
  auto is_close_fraction(const NumericType& a,  
                         const NumericType& b,  
                         const NumericType& tol) noexcept -> bool  
  {  
    using std::fabs;  
  
    auto result_is_ok = bool { };  
  
    NumericType delta { };  
  
    if(b == static_cast<NumericType>(0))  
    {  
      delta = fabs(a - b); // LCOV_EXCL_LINE  
  
      result_is_ok = (delta <= tol); // LCOV_EXCL_LINE  
    }  
    else  
    {  
      delta = fabs(1 - (a / b));  
  
      result_is_ok = (delta <= tol);  
    }  
  
    return result_is_ok;  
  }  
} // namespace local  
  
#define MY_REQUIRE_CLOSE(a, b, tol) local::is_close_fraction((a), (b), (tol))  
  
template<typename T>  
T f(T x, T y)  
{  
    return (x * y) / ((x + y) * (x - y));  
}  
  
template<typename T>  
std::vector<T> grad_f_a(T x, T y)  
{  
    T f_x = static_cast<T>(y * (-pow(x, 2) - pow(y, 2))  
                           / (pow(x, 4) - 2 * pow(x, 2) * pow(y, 2) + pow(y, 4)));  
    T f_y = static_cast<T>(x * (pow(x, 2) + pow(y, 2))  
                           / (pow(x, 4) - 2 * pow(x, 2) * pow(y, 2) + pow(y, 4)));  
    return {f_x, f_y};  
}  
  
template<typename T>  
constexpr T boost_close_tol(T scale_factor = 1e5)  
{  
    return std::numeric_limits<T>::epsilon() * scale_factor;  
}  
  
template<typename T, size_t order = 1>  
using local_rvar_type = boost::math::differentiation::reverse_mode::rvar<T, order>;  
  
template<typename T>  
auto do_something() -> bool  
{  
    bool result_is_ok { true };  
  
    //RandomSample<T> rng{-1, 1};  
    T x = 1.1224;   //rng.next();  
    T y = 5.213414; //rng.next();  
  
    local_rvar_type<T, 1>      x_ad              = x;  
    local_rvar_type<T, 1>      y_ad              = y;  
  
    //T               fv                = f(x, y);  
    local_rvar_type<T, 1> f_ad              = f(x_ad, y_ad);  
    auto                  grad_f_analytical = ::grad_f_a(x, y);  
    /* intended use case */  
    f_ad.backward(); // <- this computes the derivatives in place  
  
    {  
        const bool result_inplace_d0_is_ok { MY_REQUIRE_CLOSE(x_ad.adjoint(), grad_f_analytical[0], boost_close_tol<T>()) };  
        const bool result_inplace_d1_is_ok { MY_REQUIRE_CLOSE(y_ad.adjoint(), grad_f_analytical[1], boost_close_tol<T>()) };  
  
        result_is_ok = (result_inplace_d0_is_ok && result_is_ok);  
        result_is_ok = (result_inplace_d1_is_ok && result_is_ok);  
    }  
  
    boost::math::differentiation::reverse_mode::gradient_tape<T, 1, BOOST_MATH_BUFFER_SIZE>& tape = boost::math::differentiation::reverse_mode::get_active_tape<T, 1>();  
    tape.zero_grad();  
  
    /* grad test */  
    auto grad_func_test_grad    = grad(f_ad, &x_ad, &y_ad); // <- this returns a copy of the gradient vector w.r.t x and t  
    /* grad_nd test */  
    auto grad_nd_func_test_grad = grad_nd<1>(f_ad, &x_ad, &y_ad); // <- same thing, but use this function for higher order derivatives  
  
    for (int i = 0; i < 2; i++)  
    {  
        const bool result_inplace_d0_is_ok { MY_REQUIRE_CLOSE(grad_func_test_grad[i], grad_f_analytical[i], boost_close_tol<T>()) };  
        const bool result_inplace_dn_is_ok { MY_REQUIRE_CLOSE(grad_nd_func_test_grad[i], grad_f_analytical[i], boost_close_tol<T>()) };  
  
        result_is_ok = (result_inplace_d0_is_ok && result_is_ok);  
        result_is_ok = (result_inplace_dn_is_ok && result_is_ok);  
    }  
  
    return result_is_ok;  
}  
  
auto main() -> int  
{  
  const bool result_is_ok { do_something<double>() };  
  
  std::stringstream strm { };  
  
  strm << "result_is_ok: " << std::boolalpha << result_is_ok;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 9

> Username: demroz  
> Created_at: 2025-08-19 15:21:08 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201195282  

should I also change the 'T's to 'RealType' or 'Real' as well to stay consistent with the forward mode library?

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2025-08-19 15:32:57 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201244098  

> should I also change the `T`s to `RealType` or `Real` as well to stay consistent with the forward mode library?  
  
Hmmm... That's a good question. I'm not a huge fan of the letter `T`, even though it's quite well-established. I like a template parameter to `TellMeSomething`, even if it's just a bit like `Real`. I'll leave `Real` or `RealType` up to you, but I do actually personally have a non-`T` preference.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2025-08-19 15:35:28 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201252494  

Also when you get into Code, then your first step is a local small-case type.  
  
I like thinkgs like  
  
```  
template<typename RealType>  
auto do_something(RealType x) -> void  
{  
  using local_real_type = RealType;  
  
  static_cast<void>(std::numeric_limits<local_real_type>::is_specialized);  
}  
```

---

## Comment 12

> Username: demroz  
> Created_at: 2025-08-19 15:56:39 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201321659  

just to be clear something like this? Because I think I got conflicting advice from @mborland  
```cpp  
template<typename RealType, size_t DerivativeOrder, typename LHS, typename RHS>  
struct add_expr  
    : public abstract_binary_expression<RealType, DerivativeOrder, LHS, RHS, add_expr<RealType, DerivativeOrder, LHS, RHS>>  
{  
    /* @brief addition  
   * rvar+rvar  
   * */  
   using real_type = RealType;  
   using order = DerivatveOrder;  
   using lhs_type = LHS;  
   using rhs_type = RHS:  
    using inner_t    = rvar_t<real_type, order - 1>;  
    explicit add_expr(const expression<real_type, order, lhs_type> &left_hand_expr,  
                      const expression<real_type, order, rhs_type> &right_hand_expr)  
        : abstract_binary_expression<real_type, order, lhs_type, rhs_type, add_expr<real_type, order, lhs_type, rhs_type>>(  
              left_hand_expr, right_hand_expr)  
    {}  
  
    inner_t              evaluate() const { return this->lhs.evaluate() + this->rhs.evaluate(); }  
    static const inner_t left_derivative(const inner_t & /*l*/,  
                                         const inner_t & /*r*/,  
                                         const inner_t & /*v*/)  
    {  
        return inner_t(1.0);  
    }  
    static const inner_t right_derivative(const inner_t & /*l*/,  
                                          const inner_t & /*r*/,  
                                          const inner_t & /*v*/)  
    {  
        return inner_t(1.0);  
    }  
};  
```

---

## Comment 13

> Username: mborland  
> Created_at: 2025-08-19 16:07:12 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201354958  

I don't like when template types are immediately redefined since it's one more thing I have to keep track of. Having say a local real type applies when you have a template inside a template class.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2025-08-19 17:25:43 UTC  
> Updated_at: 2025-08-19 17:26:18 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3201585665  

> Because I think I got conflicting advice from @mborland  
  
When in doubt, do what Matt says.  
  
I'm not going to haggle about a secondary local type. I think the `UpperLowerTepmlateParameter` thing is a Boost preference, though.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2025-08-20 14:57:46 UTC  
> Updated_at: 2025-08-20 15:05:57 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3206773723  

Hi @demroz I ran your code in a first pass through MSVC's Intellisense and also GCC/clang. There were some very trivial findings with warnings, as desctibed [here](https://github.com/demroz/math/pull/5). I went ahead and PR-ed it before writing trivial stuff like: Put a cast here, don't have a semi-colon there and whatnot.  
  
There is no real content there in my PR, just handling some trivial warnings.  
  
I have also worked much more with taking derivatives in one or more variables.  
  
I have encountered the clash between Multiprecision types and the `rval` types. I think down the road for this we might consider _bindings_ for all of the basic operations and `<cmath>` functions. We made numerous bindings over the years to make Multiprecision types _play_ _nice_ with Boost.Math. In fact, we had to since Multiprecision types are also expression template enabled (when parameterized to do so).  
  
But if we go down the bindings-road, we will need a big long bindings file. That would be something I would kick down the road for a while. First get the gradients working, then we could talk about how they could/should evolve.  
  
Cc: @mborland

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2025-08-20 15:04:19 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3206797039  

At this point, I personally am satisfied with the code.  
  
I seem to have missed additions to the docs for this potential library. Perhaps I missed that, or we need to have the docs updated.  
  
I'm also missing code coverage. I think it's not set up to run coverage on forks. I wonder @mborland do you know why @demroz PRs are not triggering coverage? Oh they are only running Drone, no GHA.

---

## Comment 17

> Username: demroz  
> Created_at: 2025-08-20 16:05:33 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3207009880  

Hi @ckormanyos I merged your changes, and I'm working on changing the template variable names to the preferred style.  
  
Could you post the example of the clash you found between `multiprecision` and `rvar`? My tests test against `boost::multiprecision::cpp_bin_float_50;` like the forward mode library, so I'm a bit surprised.  
  
As far as the docs are concerned, you haven't missed them. I was waiting to write them to see if there were any major revisions needed. Given that it doesn't look to be the case, I'll work on them and have them in the next few days or this weeked.

---

## Comment 18

> Username: mborland  
> Created_at: 2025-08-20 16:53:58 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3207261181  

@ckormanyos since this is his first PR all the GitHub actions runs need to be approved manually on every run.   
  
@demroz I don't intend to ask for any other changes at this point either. I look through the docs once you say they're good to go.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2025-08-21 06:46:42 UTC  
> Updated_at: 2025-08-21 06:50:58 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3209218210  

Hi @demroz I have another query, and it might be worth looking into.  
  
The program below computes the gradient (in this case first derivative) of a real-valued function in one variable, $x$.  
  
I'm playing around with a very beautiful identity which is:  
  
$$  
\frac{\frac{d}{dx}\Gamma (x)}{\Gamma (x)}=\psi (x)  
$$  
  
It leads to all kinds of beautiful numbers, especially when $x$ is an integer value, such as $2$ or $5$.  
  
When I put a value like $5.01$ in the progam, then I get a nice, correct value. But for a whole number like $5.0$, I get zero. And I think the derivative should be non-zero.  
  
Can you see what I mean? In the code? Is this the expected behavior?  
  
```  
#include <boost/math/differentiation/autodiff_reverse.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <sstream>  
#include <vector>  
  
namespace local  
{  
  template<typename NumericType>  
  auto is_close_fraction(const NumericType& a,  
                         const NumericType& b,  
                         const NumericType& tol) noexcept -> bool  
  {  
    using std::fabs;  
  
    auto result_is_ok = bool { };  
  
    NumericType delta { };  
  
    if(b == static_cast<NumericType>(0))  
    {  
      delta = fabs(a - b); // LCOV_EXCL_LINE  
  
      result_is_ok = (delta <= tol); // LCOV_EXCL_LINE  
    }  
    else  
    {  
      delta = fabs(1 - (a / b));  
  
      result_is_ok = (delta <= tol);  
    }  
  
    return result_is_ok;  
  }  
} // namespace local  
  
#define MY_REQUIRE_CLOSE(a, b, tol) local::is_close_fraction((a), (b), (tol))  
  
template<typename T>  
T test_function(T x)  
{  
  return boost::math::tgamma(x);  
}  
  
template<typename T>  
T ctrl_function(T x)  
{  
  return boost::math::digamma(x);  
}  
  
template<typename T>  
constexpr T boost_close_tol(T scale_factor = 1024)  
{  
  return std::numeric_limits<T>::epsilon() * scale_factor;  
}  
  
template<typename T, size_t order = 1>  
using local_rvar_type = boost::math::differentiation::reverse_mode::rvar<T, order>;  
  
template<typename T>  
auto do_something() -> bool  
{  
  // Let's try 5.0L here.  
  T x { static_cast<T>(5.01L) };  
  
  local_rvar_type<T, 1> x_ad = x;  
  
  //T                   fv                = test_function(x);  
  local_rvar_type<T, 1> f_ad              = test_function(x_ad);  
  
  /* intended use case */  
  f_ad.backward(); // <- this computes the derivatives in place  
  
  using local_gradient_tape_type = boost::math::differentiation::reverse_mode::gradient_tape<T, 1, BOOST_MATH_BUFFER_SIZE>;  
  
  local_gradient_tape_type& tape = boost::math::differentiation::reverse_mode::get_active_tape<T, 1>();  
  tape.zero_grad();  
  
  /* grad test */  
  auto grad_func_test_grad = grad(f_ad, &x_ad); // <- this returns a copy of the gradient vector w.r.t x and t  
  
  const T d_gamma { grad_func_test_grad[std::size_t { UINT8_C(0) }] };  
  
  const T d_gamma_over_gamma { d_gamma / test_function(x) };  
  
  const T ctrl { ctrl_function(x) };  
  
  const bool result_is_ok { MY_REQUIRE_CLOSE(d_gamma_over_gamma, ctrl, boost_close_tol<T>()) };  
  
  return result_is_ok;  
}  
  
auto main() -> int  
{  
  const bool result_is_ok { do_something<double>() };  
  
  std::stringstream strm { };  
  
  strm << "result_is_ok: " << std::boolalpha << result_is_ok;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2025-08-21 07:21:52 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3209327880  

Another query on syntax...  
  
Why don't lines like [this](https://github.com/demroz/math/blob/602f227398a02646dab1243ba692a7ce3381cd30/include/boost/math/differentiation/autodiff_reverse.hpp#L580) or [this other](https://github.com/demroz/math/blob/602f227398a02646dab1243ba692a7ce3381cd30/include/boost/math/differentiation/autodiff_reverse.hpp#L613) use `(auto& xi : x)` in other words with a reference?  
  
I am not sure, but I think a reference _might_ be OK here and better for UDTs like Multiprecision types.

---

## Comment 21

> Username: demroz  
> Created_at: 2025-08-21 09:15:27 UTC  
> Updated_at: 2025-08-21 09:33:53 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3209687982  

@ckormanyos wow, I'm surprised that the calls to tgamma work at all and produce correct derivatives anywhere. I was previously getting many errors related to expression templates. To answer your question however, it seems like in the calculation of gamma for whole numbers there is a break in the autodiff graph. My best guess would be line 144 in ```gamma.hpp```  
  
```  
result *= unchecked_factorial<T>(static_cast<unsigned>(itrunc(z, pol) - 1));  
```  
  
In any case, if you check the [add_special_function_overloads](https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/autogenerated_gamma.hpp) branch on my fork, you can see a working header file for gamma functions. This solution is minimally invasive to the rest of the library, and doesn't need modifications of the special function calculations themselves, but requires an overload to the `expression` type and a derivative definition. I tested it locally, and if you include that header file, and replace the boost::math::tgamma call with `boost::math::differentiation::reverse_mode::tgamma` you get the correct derivative for 5.0. I started writing the code to support these, but quickly realized how much boilerplate is needed. I think most of this code can be autogenerated using some python scripts though. It could be a future addition to the library, or support could be incrementally added as users need special functions to autodiff over.  
  
as far as the `auto xi : x` calls, I noticed these last night as well and have the fixes on my local machine but haven't pushed them yet

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2025-08-21 11:09:11 UTC  
> Updated_at: 2025-08-21 11:09:45 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210080548  

> if you check the [add_special_function_overloads](https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/autogenerated_gamma.hpp) branch on my fork, you can see a working header file for gamma functions. This solution is minimally invasive to the rest of the library  
  
Hmmm... are we thinking through this right? I was wondering if adding boilerplate code that binds global add/sub/mul/div, comparison operators, all `<cmath>` functions, etc. to expression template-versions of `rval` would be sufficient to handle all of specfun at once, without dealing with each individual function. Now these binding template-functions might end up in `namespace boost::math::differentiation` or whatever the right place is, and they would be found via ADL. That would be (in my best first-guess) something like 2,000-4000 lines, but one-time only. If something like this works or would work, I'd rather do it once than for each individual function.  
  
To be fair though, I have not thought this through in its entirety.  
  
Your thoughts Matt (@mborland)?

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2025-08-21 11:10:57 UTC  
> Updated_at: 2025-08-21 11:12:28 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210085497  

> I'm surprised that the calls to `tgamma()` work at all and produce correct derivatives anywhere  
  
I cheated. Calls to `tgamma()` itself are shielded. In my test code in that sample, the Gamma function itself is called from within the _wrapper_ of `test_function()`.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2025-08-21 11:18:44 UTC  
> Updated_at: 2025-08-21 11:19:36 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210106878  

>> I'm surprised that the calls to `tgamma()` work at all and produce correct derivatives anywhere  
  
> I cheated. Calls to `tgamma()` itself are shielded. In my test code in that sample, the Gamma function itself is called from within the wrapper of `test_function()`.  
  
You might be remembering multiple-precision calls. My example at the moment uses built-in `double`. It think the multiprecision types will need syntactic sugar (i.e., bindings) to seemlessla interoperate with `rval`.  
  
For instance if I instantiate my test program for `boost::multiprecision::cpp_dec_float_50` a whole bunch of stuff goes haywire indeed.  
  
But, again, I'm not entirely sure if or how to write the bindings in the best, most efficient and clear way.

---

## Comment 25

> Username: mborland  
> Created_at: 2025-08-21 11:19:21 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210108575  

> > if you check the [add_special_function_overloads](https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/autogenerated_gamma.hpp) branch on my fork, you can see a working header file for gamma functions. This solution is minimally invasive to the rest of the library  
>   
> Hmmm... are we thinking through this right? I was wondering if adding boilerplate code that binds global add/sub/mul/div, comparison operators, all `<cmath>` functions, etc. to expression template-versions of `rval` would be sufficient to handle all of specfun at once, without dealing with each individual function. Now these binding template-functions might end up in `namespace boost::math::differentiation` or whatever the right place is, and they would be found via ADL. That would be (in my best first-guess) something like 2,000-4000 lines, but one-time only. If something like this works or would work, I'd rather do it once than for each individual function.  
>   
> To be fair though, I have not thought this through in its entirety.  
>   
> Your thoughts Matt (@mborland)?  
  
To me it sounded like the wrappers you are describing aren't feasible, which is why he has the boilerplate versions of these functions. Same thing happened in the forward autodiff library. I told him the current functions he supports is a good start and there's room for expansion should people request it.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2025-08-21 11:22:00 UTC  
> Updated_at: 2025-08-21 11:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210115866  

> To me it sounded like the wrappers you are describing aren't feasible,  
  
Uggghhh yuck. This is a hard question, I wouldn't know how to answer it, but do you know why it's not feasible? I think I took a local try at it and ran into trouble right away and was wondering if this is the non-feasibility? I could not even write the constructors I needed. And global assignment operators aren't allowed. So I gave up, at least on the quick-shot-attempt.  
  
I just thought somewhere out there might be a generic solution instead of a function-by-function dedicated effort...

---

## Comment 27

> Username: ckormanyos  
> Created_at: 2025-08-21 11:25:30 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210125512  

I suppose this is going to sound literally crazy. But in some kind of world, you could make a `number` multiprecision backend for `rval`. But again you'd need a few thousand lines for the `eval_whatever()` functions. And I do not know if this is feasible either?

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2025-08-21 11:57:40 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210297739  

Well I can get it compiling for `cyl_bessel_j`, but it gives me the $0.0$ answer for derivatives. I wonder if it's possible to see where the call graph is breaking? And why?

---

## Comment 29

> Username: ckormanyos  
> Created_at: 2025-08-21 11:59:48 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210304912  

Also @demroz I see lines like [this](https://github.com/demroz/math/blob/b53cfc25c9e841c41fa7000ea9fa5361eb211427/include/boost/math/differentiation/autodiff_reverse.hpp#L703) at various places.  
  
I think you might know the size of the vector up-front, although I'm not sure. If the size of the vector is known, you might want to `reserve()` enough place in the vector to avoid reallocation costs on `push_back()`?

---

## Comment 30

> Username: demroz  
> Created_at: 2025-08-21 12:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210337710  

@ckormanyos   
> > if you check the [add_special_function_overloads](https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/autogenerated_gamma.hpp) branch on my fork, you can see a working header file for gamma functions. This solution is minimally invasive to the rest of the library  
>   
> Hmmm... are we thinking through this right? I was wondering if adding boilerplate code that binds global add/sub/mul/div, comparison operators, all `<cmath>` functions, etc. to expression template-versions of `rval` would be sufficient to handle all of specfun at once, without dealing with each individual function. Now these binding template-functions might end up in `namespace boost::math::differentiation` or whatever the right place is, and they would be found via ADL. That would be (in my best first-guess) something like 2,000-4000 lines, but one-time only. If something like this works or would work, I'd rather do it once than for each individual function.  
>   
> To be fair though, I have not thought this through in its entirety.  
>   
> Your thoughts Matt (@mborland)?  
  
It is very much possible to write bindings to add/sub/mul/div and `cmath` functions, which is in fact what I've done. All stl/basic operations are currently supported. If the code for special functions is written carefully with consideration for autodiff, you can in fact get the correct derivatives for these special functions using autodiff. The problem is that there are chunks of the special function code that are not written with autodiff in mind. The example you posted above is a good one, it looks to me like a call to tgamma(x) for x=5.0 breaks the autodiff chain by casting my rvar type to an unsigned int and recasting it back to rvar. This doesn't work.  
  
another good example is whenever there is an internal call to a special function doing something like this:  
```cpp  
sin_pi(2.0*x);  
```  
  
This becomes a problem because you are no longer calling `sin_pi` on a `rvar` type, but on a `mult_expr<const, rvar>`. Then inside of `sin_pi` there is code like `x = -x;` which wont work because x is type `mult_expr<const,rvar>` and `-x` is type `neg_expr<mult_expr<const,rvar>>` also you have calls like `(x < T(0.5))` which don't work either because `mult_expr<const,rvar>` isn't real constructible. Code like this is all over the special functions implementations, which unfortunately forces you to write the boilerplate heavy code.  
  
My opinion: There are very legitimate reasons to want to autodiff through special functions. I've done this in the past for physical simulations that I want to optimize over. These situations aren't particularly common though. It isn't difficult per say to write specialized 'autodiff compatible' bindings to special functions, there is just a lot of them.

---

## Comment 31

> Username: demroz  
> Created_at: 2025-08-21 12:29:46 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210405540  

> Well I can get it compiling for `cyl_bessel_j`, but it gives me the 0.0 answer for derivatives. I wonder if it's possible to see where the call graph is breaking? And why?  
  
could you send me the code? I'll look at it. The way I tend to try to find where the graph breaks is step through with a debugger and see if there is an unexpected call. So for tgamma, what I stepped through the call until i saw i was inside the boost version `itrunc(z, pol)` which was unexpected to me because I overload `itrunc`. Sorry that I don't have a better answer for you. I don't really know of good tools to visualize these sorts of graphs  
  
  
  
> Also @demroz I see lines like [this](https://github.com/demroz/math/blob/b53cfc25c9e841c41fa7000ea9fa5361eb211427/include/boost/math/differentiation/autodiff_reverse.hpp#L703) at various places.  
>   
> I think you might know the size of the vector up-front, although I'm not sure. If the size of the vector is known, you might want to `reserve()` enough place in the vector to avoid reallocation costs on `push_back()`?  
  
this is a good catch. I'll update

---

## Comment 32

> Username: ckormanyos  
> Created_at: 2025-08-21 12:50:58 UTC  
> Updated_at: 2025-08-21 12:51:18 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210473689  

>> I think you might know the size of the vector up-front, although I'm not sure  
  
> this is a good catch. I'll update  
  
I'm not going to force the issue here, but you could (theoretically) use `std::array<T, N>` and then (with a lot of code modifications) use constant-sized arrays in your internals. But then you would need to use `const size_t N` as a template parameter.  
  
You'd have to do a lot of code changing. It might not be worth it, but it would help `constexpr`-gradients a lot if that's a design goal.

---

## Comment 33

> Username: ckormanyos  
> Created_at: 2025-08-21 12:54:51 UTC  
> Updated_at: 2025-08-21 12:55:12 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210486870  

> My opinion: There are very legitimate reasons to want to autodiff through special functions. I've done this in the past for physical simulations that I want to optimize over. These situations aren't particularly common though. It isn't difficult per say to write specialized 'autodiff compatible' bindings to special functions, there is just a lot of them.  
  
I think it might be a rather clear case for even stronger motivation.  
  
Look at it from this perspective.  
  
- I take the `autodiff` code off the rack  
- Ok great I want to make some gradients.  
- So I put it onto some random special-func in Boost and it fails.  
  
So there's nothing, let's say, _magic_ about Boost.Math's special functions. So who's to say when and where the differentiation code will simply fail unexpectedly again. Maybe some clients write some perfeclty reasonable numerical code and `autodiff` just fails? I suspect this is the time to work out this kind of stuff, not later when the potential library is out in the wild.

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2025-08-21 12:59:16 UTC  
> Updated_at: 2025-08-21 13:00:13 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210501365  

Here is the failing `cyl_bessel_j` code. I used the inner-type `value_type` to call the special function and it still fails unexpectedly.  
  
I am trying to take the derivative with respect to $x$ of  
  
$$  
J_{0.25}(x){\vert}_{1.25}  
$$  
  
```  
#include <boost/math/differentiation/autodiff_reverse.hpp>  
#include <boost/math/special_functions/bessel.hpp>  
  
#include <algorithm>  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <sstream>  
#include <vector>  
  
namespace local  
{  
  template<typename NumericType>  
  constexpr auto is_close_fraction(const NumericType& a,  
                                   const NumericType& b,  
                                   const NumericType& tol) noexcept -> bool  
  {  
    auto result_is_ok = bool { };  
  
    NumericType delta { };  
  
    using std::abs;  
  
    if(b == static_cast<NumericType>(0))  
    {  
      delta = abs(a - b); // LCOV_EXCL_LINE  
  
      result_is_ok = (delta <= tol); // LCOV_EXCL_LINE  
    }  
    else  
    {  
      delta = abs(1 - (a / b));  
  
      result_is_ok = (delta <= tol);  
    }  
  
    return result_is_ok;  
  }  
} // namespace local  
  
template<typename NumericType>  
static constexpr auto MY_REQUIRE_CLOSE(const NumericType& a, const NumericType& b, const NumericType& tol) -> bool  
{  
  return local::is_close_fraction(a, b, tol);  
}  
  
template<typename FloatType, typename RvalType>  
RvalType test_function(FloatType vu, RvalType x)  
{  
  using local_value_type = typename RvalType::value_type;  
  const local_value_type result_value { boost::math::cyl_bessel_j(vu, static_cast<local_value_type>(x)) };  
  
  return RvalType { result_value };  
}  
  
template<typename T>  
T ctrl_function(T vu, T x)  
{  
  return    boost::math::cyl_bessel_j(vu - 1.0, x)  
         - (vu * boost::math::cyl_bessel_j(vu, x)) / x;  
}  
  
template<typename T>  
constexpr T boost_close_tol(T scale_factor = 1024)  
{  
  return std::numeric_limits<T>::epsilon() * scale_factor;  
}  
  
template<typename T, size_t order = 1>  
using local_rvar_maker_type = boost::math::differentiation::reverse_mode::rvar<T, order>;  
  
template<typename T>  
auto do_something() -> bool  
{  
  // Let's try 5.0L here.  
  T vu { static_cast<T>(0.25L) };  
  T x  { static_cast<T>(1.25L) };  
  
  using local_rvar_type = local_rvar_maker_type<T, 1>;  
  
  local_rvar_type x_ad = x;  
  
  //T             fv                = test_function(x);  
  local_rvar_type f_ad              = test_function(vu, x_ad);  
  
  /* intended use case */  
  f_ad.backward(); // <- this computes the derivatives in place  
  
  using local_gradient_tape_type = boost::math::differentiation::reverse_mode::gradient_tape<T, 1, BOOST_MATH_BUFFER_SIZE>;  
  
  local_gradient_tape_type& tape = boost::math::differentiation::reverse_mode::get_active_tape<T, 1>();  
  tape.zero_grad();  
  
  /* grad test */  
  auto grad_func_test_grad = grad(f_ad, &x_ad); // <- this returns a copy of the gradient vector w.r.t x and t  
  
  const T deriv_val { grad_func_test_grad[std::size_t { UINT8_C(0) }] };  
  
  const T ctrl { ctrl_function(vu, x) };  
  
  const bool result_is_ok { MY_REQUIRE_CLOSE(deriv_val, ctrl, boost_close_tol<T>()) };  
  
  return result_is_ok;  
}  
  
auto main() -> int  
{  
  const bool result_is_ok { do_something<double>() };  
  
  std::stringstream strm { };  
  
  strm << "result_is_ok: " << std::boolalpha << result_is_ok;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 35

> Username: demroz  
> Created_at: 2025-08-21 13:05:35 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210526778  

> Here is the failing `cyl_bessel_j` code. I used the inner-type `value_type` to call the special function and it still fails unexpectedly.  
>   
> I am trying to take the derivative with respect to x of  
  
That code breaks the autodiff chain before you even call `boost::math::cyl_bessel_j` with `static_cast<local_value_type>(x)`. You're casting the `rvar` type to a double

---

## Comment 36

> Username: demroz  
> Created_at: 2025-08-21 13:12:26 UTC  
> Updated_at: 2025-08-21 13:23:34 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210553090  

> > My opinion: There are very legitimate reasons to want to autodiff through special functions. I've done this in the past for physical simulations that I want to optimize over. These situations aren't particularly common though. It isn't difficult per say to write specialized 'autodiff compatible' bindings to special functions, there is just a lot of them.  
>   
> I think it might be a rather clear case for even stronger motivation.  
>   
> Look at it from this perspective.  
>   
> * I take the `autodiff` code off the rack  
> * Ok great I want to make some gradients.  
> * So I put it onto some random special-func in Boost and it fails.  
>   
> So there's nothing, let's say, _magic_ about Boost.Math's special functions. So who's to say when and where the differentiation code will simply fail unexpectedly again. Maybe some clients write some perfeclty reasonable numerical code and `autodiff` just fails? I suspect this is the time to work out this kind of stuff, not later when the potential library is out in the wild.  
  
Ok i can work on this, but it may take a little while. Here is how I see this being implemented. We have a `.json` file (i dont actually have a filetype preference) with a list of "special functions." This file contains basically the name of the function, boost namespace function call and its derivative information, and maybe some sort of info about the test cases needed. We have some python scripts that generate this code: check [here](https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/generate_expression_templated_headers.py) for a rough example that doesn't work, but kind of highlights the idea of what I'm talking about.  This is going to generate something thats ~10k lines of code i think. Which is why i want to have this be done automatically though a script. i'd rather debug that in the future than the c++ code.

---

## Comment 37

> Username: ckormanyos  
> Created_at: 2025-08-21 14:16:53 UTC  
> Updated_at: 2025-08-21 14:17:45 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210803389  

> Ok i can work on this, but it may take a little while. Here is how I see this being implemented. We have a .json file (i dont actually have a filetype preference) with a list of "special functions." This file contains basically the name of the function, boost namespace function call and its derivative information, and maybe some sort of info about the test cases needed. We have some python scripts that generate this code:   
  
I need to be rather direct. This does not seem like a good solution moving forward. It is overly complicated and non-permanent in the sense of difficult to maintain.  
  
As a potential client of this new library, I simply want it to work for my code. I mention again, there is absolutely nothing special about Boost's special functions except the word special. Other than that, it is just ordinary, vanilla template code. This kind of code could come from literally _anywhere_.  
  
I see two possible ways out of this pickle.  
  
- Get the `autodiff` code working on any code  
- Or tell us where and why it breaks so we can fix this together.  
  
A lot of our clients write detailed code. It's going to be difficult when you get code out in the wild and have to say, well, I guess your client cod3 was just too complex for `autodiff`. Remember our clients are extremely demanding.  
  
The first step is to find out where, for instance, `cyl_bessel_j` breaks the code. Why? Can it be fixed?

---

## Comment 38

> Username: demroz  
> Created_at: 2025-08-21 14:40:49 UTC  
> Updated_at: 2025-08-21 15:22:20 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3210893217  

> > Ok i can work on this, but it may take a little while. Here is how I see this being implemented. We have a .json file (i dont actually have a filetype preference) with a list of "special functions." This file contains basically the name of the function, boost namespace function call and its derivative information, and maybe some sort of info about the test cases needed. We have some python scripts that generate this code:  
>   
> I need to be rather direct. This does not seem like a good solution moving forward. It is overly complicated and non-permanent in the sense of difficult to maintain.  
>   
> As a potential client of this new library, I simply want it to work for my code. I mention again, there is absolutely nothing special about Boost's special functions except the word special. Other than that, it is just ordinary, vanilla template code. This kind of code could come from literally _anywhere_.  
>   
> I see two possible ways out of this pickle.  
>   
> * Get the `autodiff` code working on any code  
> * Or tell us where and why it breaks so we can fix this together.  
>   
> A lot of our clients write detailed code. It's going to be difficult when you get code out in the wild and have to say, well, I guess your client cod3 was just too complex for `autodiff`. Remember our clients are extremely demanding.  
>   
> The first step is to find out where, for instance, `cyl_bessel_j` breaks the code. Why? Can it be fixed?  
  
So I raised this issue to @mborland in #1281.  I just looked at the implementation of `cyl_bessel_j`. The first point of failure is:   
```cpp  
template <class T, class Policy>  
BOOST_MATH_GPU_ENABLED inline T asymptotic_bessel_j_large_x_2(T v, T x, const Policy& pol)  
{  
   // See A&S 9.2.19.  
   BOOST_MATH_STD_USING  
   // Get the phase and amplitude:  
   T ampl = asymptotic_bessel_amplitude(v, x);  
   if (0 == ampl)   
      return ampl;  // shortcut.  
   T phase = asymptotic_bessel_phase_mx(v, x);  
   BOOST_MATH_INSTRUMENT_VARIABLE(ampl);  
   BOOST_MATH_INSTRUMENT_VARIABLE(phase);  
   //  
   // Calculate the sine of the phase, using  
   // sine/cosine addition rules to factor in  
   // the x - PI(v/2 + 1/4) term not added to the  
   // phase when we calculated it.  
   //  
   BOOST_MATH_INSTRUMENT_CODE(cos(phase));  
   BOOST_MATH_INSTRUMENT_CODE(cos(x));  
   BOOST_MATH_INSTRUMENT_CODE(sin(phase));  
   BOOST_MATH_INSTRUMENT_CODE(sin(x));  
   T cx = cos(x);  
   T sx = sin(x);  
   T ci = boost::math::cos_pi(v / 2 + 0.25f, pol); // <- right here   
   T si = boost::math::sin_pi(v / 2 + 0.25f, pol);  
   T sin_phase = cos(phase) * (cx * ci + sx * si) - sin(phase) * (sx * ci - cx * si);  
   BOOST_MATH_INSTRUMENT_VARIABLE(sin_phase);  
   return sin_phase * ampl;  
}  
```  
  
`boost::math::cos_pi` is no longer called on `rvar` but on `add_expr<div_expr<rvar,float>,float>`. `T` in this case is `rvar`, inside `boost::math:cos_pi` there are a few places that don't work with expression templates the way I'm using them. Looking at the code for `cos_pi`  
```cpp  
BOOST_MATH_GPU_ENABLED T cos_pi_imp(T x, const Policy&)  
{  
   BOOST_MATH_STD_USING // ADL of std names  
   // cos of pi*x:  
   bool invert = false;  
   if(fabs(x) < T(0.25)) // <- non rvar expressions are not numeric type constructible.  
      return cos(constants::pi<T>() * x); // this can't work now because the return type is T, but cos(...) returns cos_expr  
   if(x < 0)  
   {  
      x = -x; //<- cannot work with an expression template because the type of x mutates  
   }  
   T rem = floor(x);  
   if(abs(floor(rem/2)*2 - rem) > boost::math::numeric_limits<T>::epsilon())  
   {  
      invert = !invert;  
   }  
   rem = x - rem;  
   if(rem > 0.5f)  
   {  
      rem = 1 - rem;  
      invert = !invert;  
   }  
   if(rem == 0.5f)  
      return 0;  
     
   if(rem > 0.25f)  
   {  
      rem = 0.5f - rem;  
      rem = sin(constants::pi<T>() * rem);  
   }  
   else  
      rem = cos(constants::pi<T>() * rem);  
   return invert ? T(-rem) : rem;  
}  
```   
There are basically two (maybe 4 now that I'm thinking about it) ways out:  
1. What I proposed above. provide overloads to special functions for the "expression" base type.  
2. Take the performance hit. Ditch expression templates. Make the `rvar` type non-mutating. This results in longer tapes. I haven't bench-marked ET vs no-et personally. I've read from some sources its 2-3x for typical use cases.  
3. I dont know if this is possible, but i can't think of a reason it shouldn't be. Manually turn off expression templates for special functions. This makes the library more complicated to use though  
4. Globally enable/disable ET with a macro.   
  
If the above solution is overtly complicated, I propose option 4. Enable/disable ET at compile time and document that ETs clash with special functions.

---

## Comment 39

> Username: ckormanyos  
> Created_at: 2025-08-21 16:19:44 UTC  
> Updated_at: 2025-08-21 16:22:56 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211282694  

> `boost::math::cos_pi` is no longer called on `rvar` but on `add_expr<div_expr<rvar,float>,float>`. `T` in this case is `rvar`, inside `boost::math:cos_pi` there are a few places that don't work with expression templates the way I'm using them. Looking at the code for `cos_pi`  
  
Now we are getting into the right discussion. Thanks @demroz for persisting with this.  
  
In a scope of larger sense, if you/we find weaknesses in Boost.Math, then we can correct them as they arise. But this means we would have to have a general consensus that specfun sequences might be wrong (or wrong enough to warrant change). We have had several new components test Math and we have often times changed Math accordingly if actual weaknesses were found.  
  
I do not know if this is the case here or not. But we are happy to change Math if the changes are justified and lead to better generic code.  
  
> If the above solution is overtly complicated, I propose option 4. Enable/disable ET at compile time and document that ETs clash with special functions.  
  
We would have to talk about this with all the Math stakeholders. And we would need to go through every specfun and change probably a few places. It might be a really good long-term goal.  
  
> 4. Globally enable/disable ET with a macro.  
  
This may very well be the best, let's say, transitional solution. We also enable/disable ET for Multiprecision `number`types. So this kind of concept is well-known to our most demanding clients.  
  
We could use a template parameter for `autodiff::et_on` or `autodiff::et_off` or something like that and document with great clarity that specfun needs `et_off`.  
  
This still leaves the larger question open: When does/does-not `autoduff`work out of the box? It looks good for polynomials up to a certain order. Discontinuities may be problematic. You might consider testing if `autodiff` is successful or fails and then (in a failure case) fall back to central-difference rules and provide a kind of `lossy_precision` flag to the caller.

---

## Comment 40

> Username: ckormanyos  
> Created_at: 2025-08-21 16:20:45 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211285344  

But for the moment, I'd recommend you add an active template parameter for `et_on`/`et_off` and we document that with great clarity. We can evolve beyond that point in the future. I always say, evolution, not revolution.  
  
Cc: @mborland

---

## Comment 41

> Username: mborland  
> Created_at: 2025-08-21 16:28:19 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211305845  

> But for the moment, I'd recommend you add an active template parameter for `et_on`/`et_off` and we document that with great clarity. We can evolve beyond that point in the future. I always say, evolution, not revolution.  
>   
>   
>   
> Cc: @mborland   
  
Do you think that should wait for a separate PR? We're so early in the boost cycle that I think that can be added in a next go. I'd stay with what we have for now.

---

## Comment 42

> Username: ckormanyos  
> Created_at: 2025-08-21 16:30:44 UTC  
> Updated_at: 2025-08-21 16:31:37 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211312586  

>> But for the moment, I'd recommend you add an active template parameter for `et_on`/`et_off`  
  
> Do you think that should wait for a separate PR?   
  
Matt (@mborland) as so often you read my mind.  
  
Yes, in the sense of evolution. Go with what you/we have tested now. Document the state of affairs. Then we can see if we get the next iteration into 1.90 or not.  
  
We need a resting point now. And maybe we can get the ET stuff in  by October or so.  
  
I like how you think.

---

## Comment 43

> Username: demroz  
> Created_at: 2025-08-21 16:45:26 UTC  
> Updated_at: 2025-08-21 16:51:11 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211368078  

Thanks guys I appreciate the feedback. I'll prioritize docs then work on the et on/off flag after docs are ready.  
  
I have some non ET autodiff code I'll also benchmark to see what kind of speedup we actually get with ET on.  
  
@ckormanyos as far as whether to modify internal implementations of special functions to support ET, that may not be the worst idea, though I think its outside the current scope of things. I'll take a look at the implementation of `cyl_bessel_j` and see how many changes would be needed and ping you. I have a gut feeling that it isn't horrible, mainly things like `x = -x` and `T((arg>0) x : -x)` stuff and creating variables from temporaries like `v/2+0.25` instead of calling on them directly

---

## Comment 44

> Username: ckormanyos  
> Created_at: 2025-08-21 16:56:12 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3211404839  

>  appreciate the feedback. I'll prioritize docs then work on the et on/off flag after docs are ready.  
  
When the docs are ready (and that is challenging) we should merge and test and re-establish CodeCoverage and get the whole package ready.  
  
Then after that settles down, look into ET on/off. Let's concentrate on what we have now. I can see a clear pathway to success on this thing, not only for ET on/off, but also for an evolutionary step in specfun and potential inclusion of MP types in `autodiff`.  
  
But for now, finish what you have and we can handle that really soon.

---

## Comment 45

> Username: jzmaddock  
> Created_at: 2025-08-22 10:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3213910498  

First off, my apologies for being AWOL, I'll try to look at this in due course... just a couple of quick observations:  
  
* specializing/binding every special function seems like a fools errand to me, there may be a few that have nice identities for the derivative where it's more efficient to take advantage of that, but we're almost moving into symbolic math territory, and that's a whole lot of work!  
* In the comment: "another good example is whenever there is an internal call to a special function doing something like this: `sin_pi(2.0*x);` ", that should never happen, we do test again multiprecision using expression templates, so we *should* be expression template safe.  It's possible that since you're operating at say `double` precision that you're taking a code branch we haven't tested against expression-templated UDT's yet, but that would be a bug at our end.  Being able to use a tool like this against arbitrary special functions seems like a good win to me.  But:  
* Mention was made of `tgamma(5)` resulting in zero derivative: as pointed out, this is down to integer arguments being treated as constant results.  It's the correct (and fast) thing to do in general, but obviously breaks a tool like this.  The right thing to do here is to specialize the lowest level function that blows up: `unchecked_factorial` in this case.  That function is used all over (not just in tgamma) so fixing that would fix a whole bunch of things in one go.  But:  
* There are many other special cases where we return a constant result: usually this is at the end point of a domain, or else say at `x = 0`, where the result is known, and might not be easily calculated by the normal methods anyway.  Again for normal scalar types it's important to filter those special cases off early, but they will potentially break an autodiff tool.  These are scattered all over, and I'm not sure how we fix them.  Possibly a trait for detecting types like this, and then some constexpr if's around problematic code.  
* And finally, running any new number type through our special functions is a *really* good test of the correctness of the implementation: the type will really get hammered into all kind of corner cases that you wouldn't find otherwise, and has been invaluable in testing our multiprecision types.

---

## Comment 46

> Username: ckormanyos  
> Created_at: 2025-08-22 14:30:15 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3214581255  

> And finally, running any new number type through our special functions is a really good test of the correctness of the implementation: the type will really get hammered into all kind of corner cases that you wouldn't find otherwise, and has been invaluable in testing our multiprecision types.  
  
Yes for sure! I'll start poking around at some potential minimal changes.  
  
But as Matt mentioned, I think we can go at this in separate, semi-planned stages. When I read what John wrote, it kind of reminded me how we usually start with the easiest functions like `tgamma`, walk up thorugh Bessel, polynomials and then harder functions.

---

## Comment 47

> Username: demroz  
> Created_at: 2025-08-24 12:35:21 UTC  
> Updated_at: 2025-08-25 00:27:12 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3218074395  

@mborland  @ckormanyos I wrote the first draft of the docs. It took a bit longer than i though it would. Please take a look at let me know if there are any changes/additions needed. Its my first time working with the boost documentation, but building the docs locally seems to change a bunch of html files for me. I didnt want to have 500+ files changed in the PR, so I've been doing   
```  
git ls-files doc/html | xargs git update-index --assume-unchanged  
```  
to ignore these changes, but some some still go through. Is there a better way of doing this? Also @jzmaddock thank you for taking a look at the library. I have a few responses to your comments below  
  
> In the comment: "another good example is whenever there is an internal call to a special function doing something like this: sin_pi(2.0*x); ", that should never happen, we do test again multiprecision using expression templates, so we should be expression template safe. It's possible that since you're operating at say double precision that you're taking a code branch we haven't tested against expression-templated UDT's yet, but that would be a bug at our end. Being able to use a tool like this against arbitrary special functions seems like a good win to me. But:  
  
Unfortunately this seems to happen quite often. See lines 64, 68, 215, of bessel.hpp, or 88/89 of detail/bessel_jy_asym.hpp. I'd need to take a more dedicated crack at `cyl_bessel_j` to see how much change would be needed to get ETs to work with this function.  
  
> Mention was made of tgamma(5) resulting in zero derivative: as pointed out, this is down to integer arguments being treated as constant results. It's the correct (and fast) thing to do in general, but obviously breaks a tool like this. The right thing to do here is to specialize the lowest level function that blows up: unchecked_factorial in this case. That function is used all over (not just in tgamma) so fixing that would fix a whole bunch of things in one go. But:  
  
> There are many other special cases where we return a constant result: usually this is at the end point of a domain, or else say at x = 0, where the result is known, and might not be easily calculated by the normal methods anyway. Again for normal scalar types it's important to filter those special cases off early, but they will potentially break an autodiff tool. These are scattered all over, and I'm not sure how we fix them. Possibly a trait for detecting types like this, and then some constexpr if's around problematic code.  
  
The special cases that return a constant result are indeed a big problem. I actually have a mechanism in my code for detecting expression types, so I think your solution makes sense.  
  
> And finally, running any new number type through our special functions is a really good test of the correctness of the implementation: the type will really get hammered into all kind of corner cases that you wouldn't find otherwise, and has been invaluable in testing our multiprecision types.  
  
Totally agree with you.

---

## Comment 48

> Username: jzmaddock  
> Created_at: 2025-08-24 17:38:25 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3218259409  

>Unfortunately this seems to happen quite often. See lines 64, 68, 215, of bessel.hpp, or 88/89 of detail/bessel_jy_asym.hpp. I'd need to take a more dedicated crack at cyl_bessel_j to see how much change would be needed to get ETs to work with this function.  
  
Ah got you, in multiprecision we fix this with:  
  
```  
namespace boost { namespace math {  
   namespace tools {  
  
      template <class T>  
      struct promote_arg;  
  
      template <class tag, class A1, class A2, class A3, class A4>  
      struct promote_arg<boost::multiprecision::detail::expression<tag, A1, A2, A3, A4> >  
      {  
         using type = typename boost::multiprecision::detail::expression<tag, A1, A2, A3, A4>::result_type;  
      };  
}}}  
```  
  
So that if any special function is called with an expression template, then it (internally/magically) converts that to the "real" type.  This means that in user code, if someone writes:  
  
```  
tgamma(a+b);  
```  
  
then it all still works, even though `a+b` may be an expression template.

---

## Comment 49

> Username: demroz  
> Created_at: 2025-08-25 07:52:22 UTC  
> Updated_at: 2025-08-25 07:53:10 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219209764  

@ckormanyos Some good news on the expression template on/off macro. I have a working version under the [add_no_expression_template_support](https://github.com/demroz/math/tree/add_no_expression_template_support/) branch. With the change   
```cpp  
#define BOOST_MATH_ET_OFF  
#include <boost/math/differentiation/autodiff_reverse.hpp>  
#include <boost/math/special_functions/bessel.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
#include <iostream>  
  
namespace rdiff = boost::math::differentiation::reverse_mode;  
  
int main()  
{  
    constexpr std::size_t N = 1;  
    using rvar              = rdiff::rvar<double, N>;  
    double x                = 2.1;  
    rvar   x_ad             = x;  
  
    auto g = boost::math::tgamma(x_ad + x_ad);  
    auto j = boost::math::cyl_bessel_j(0.25, x_ad + x_ad / 2);  
  
    std::cout << "tgamma(x + x) = " << g << "\n";  
    std::cout << "J_0.25(x) = " << j << "\n";  
  
    auto &tape = rdiff::get_active_tape<double, 1>();  
  
    g.backward();  
    std::cout << "d/dx tgamma(x+x), autodiff = " << x_ad.adjoint()  
              << " expected = " << 2 * boost::math::tgamma(2 * x) * boost::math::digamma(2 * x)  
              << std::endl;  
  
    tape.zero_grad();  
    j.backward();  
  
    std::cout << "d/dx J_0.25(x+x/2), autodiff = " << x_ad.adjoint() << " expected = "  
              << 3.0 / 4.0  
                     * (boost::math::cyl_bessel_j(-0.75, 3. * x / 2.)  
                        - boost::math::cyl_bessel_j(1.25, 3. * x / 2.))  
              << std::endl;  
  
    return 0;  
}  
  
```  
  
produces the expected gradients. `tgamma` on integer values is still problematic however. I'll submit a different pr with this change after the docs are approved.

---

## Review 50 [Commented]

> Username: mborland  
> Created_at: 2025-08-25 08:27:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1302#pullrequestreview-3150377379  

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 126 |+ 
 127 |+ Reverse mode autodiff is a header-only C++ library the [@https://en.wikipedia.org/wiki/Automatic_differentiation
 128 |+ automatic differentiation] (reverse mode) of mathematical functions. This implementation builds a computational graph known as a tape, which sotres all operations and their corresponding derivatives. The total gradients are then computed by reverse accumulation via the chain rule.
```

> Username: mborland  
> Created_at: 2025-08-25 08:14:01 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297426638  

```diff  
-automatic differentiation] (reverse mode) of mathematical functions. This implementation builds a computational graph known as a tape, which sotres all operations and their corresponding derivatives. The total gradients are then computed by reverse accumulation via the chain rule.  
+automatic differentiation] (reverse mode) of mathematical functions. This implementation builds a computational graph known as a tape, which stores all operations and their corresponding derivatives. The total gradients are then computed by reverse accumulation via the chain rule.  
```

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
  18 |+     /* autodiff variable of type RealType (numeric types), stores derivatives up to DerivativeOrder*/
  19 |+     template<typename RealType, size_t DerivativeOrder = 1>
  20 |+     class rvar : public expression<RealType, DerivativeOrder, rvar<RealType, DerivativeOrder>> {
```

> Username: mborland  
> Created_at: 2025-08-25 08:15:40 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297430057  

I would add a brief explanation of what `expression` is since it's in the documented signature.

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
  27 |+       rvar();
  28 |+       // construct from numeric type
  29 |+       rar(const RealType value);
```

> Username: mborland  
> Created_at: 2025-08-25 08:15:53 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297430470  

```diff  
-      rar(const RealType value);  
+      rvar(const RealType value);  
```

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
  61 |+ 
  62 |+     // gradient tape holds the computational graph
  63 |+     template<typename RealType, size_t DerivativeOrder, size_t buffer_size = BOOST_MATH_BUFFER_SIZE>
```

> Username: mborland  
> Created_at: 2025-08-25 08:17:06 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297433357  

Add a definition of what `BOOST_MATH_BUFFER_SIZE` is prior to use.

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
  82 |+ 
  83 |+     // tape access
  84 |+     template<typenametemplate<typename RealType, size_t DerivativeOrder>
```

> Username: mborland  
> Created_at: 2025-08-25 08:17:38 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297434596  

```diff  
-    template<typenametemplate<typename RealType, size_t DerivativeOrder>  
+    template<typename template<typename RealType, size_t DerivativeOrder>>  
```

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 155 |+ 1. Reverse mode auto-diff is exceptionally efficient for computing the gradient of functions mapping from high to low dimensional space, f : [real][super n][rarr][real]. Unlike finite differences or forward mode autodiff which scale with the number of input variables, reverse mode autodiff calculates the entire gradient vector in time proportional to the original function's evaluation time.
 156 |+ 
 157 |+ 2. It is possible to compute higher order derivatives by applying reverse mode over reverse mode differentiation. The backward function builds a new computational tape over the gradient computation. This approach is conceptually sound, but it can become computationally expensive very quickly. Forward mode autodiff is generally preferrable when computing higher order derivatives.
```

> Username: mborland  
> Created_at: 2025-08-25 08:19:21 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297438047  

Over forward mode?

> Username: demroz  
> Created_at: 2025-08-25 10:48:38 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297773216  

No. I apply reverse mode over reverse mode for higher order derivatives.

> Username: mborland  
> Created_at: 2025-08-25 10:56:25 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297791793  

I would add that explanation. As written "applying reverse mode over reverse mode differentiation" reads awkwardly.

> Username: demroz  
> Created_at: 2025-08-25 11:03:26 UTC  
> Updated_at: 2025-08-25 11:03:45 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297810014  

updated

> Username: mborland  
> Created_at: 2025-08-25 11:09:26 UTC  
> Updated_at: 2025-08-25 11:09:27 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297820507  

Much better. Thanks

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 317 |+       double learning_rate = 1e-3;
 318 |+ 
 319 |+ The learning rate is a tunable parameter determining the "velocity" with which we descend to a solution.
```

> Username: mborland  
> Created_at: 2025-08-25 08:22:32 UTC  
> Updated_at: 2025-08-25 08:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2297444728  

I would add in an explanation of why we wouldn't want a higher learning rate, and maybe some recommended values or range of values.


---

## Comment 51

> Username: ckormanyos  
> Created_at: 2025-08-25 09:00:00 UTC  
> Updated_at: 2025-08-25 09:02:18 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219421529  

> @ckormanyos Some good news on the expression template on/off macro. I have a working version under the [add_no_expression_template_support](https://github.com/demroz/math/tree/add_no_expression_template_support/) branch.   
  
Wow this seems very positive. I've also followed your discussion with John. I am of the opinion that, maybe down the road, we will get most or at least some of specfun working with both ET on as well as off.  
  
> With the change  
  
```  
BOOST_MATH_ET_OFF  
```  
  
Hmmm I was wondering if you would like to use a PP-definition that has one more word in it. Maybe someday, there will be several kinds of ETs in Math. What do you think about `BOOST_MATH_DIFFERENTIATION_ET_OFF` or `...ON`?

---

## Comment 52

> Username: demroz  
> Created_at: 2025-08-25 09:08:45 UTC  
> Updated_at: 2025-08-25 09:12:24 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219450674  

> > @ckormanyos Some good news on the expression template on/off macro. I have a working version under the [add_no_expression_template_support](https://github.com/demroz/math/tree/add_no_expression_template_support/) branch.  
>   
> Wow this seems very positive. I've also followed your discussion with John. I am of the opinion that, maybe down the road, we will get most or at least some of specfun working with both ET on as well as off.  
  
I think that the `promote_args` is key for this to work. I tried something like that earlier this morning, and it seemed to fix some things but the compiler still complains in various places  
  
> > With the change  
>   
> ```  
> BOOST_MATH_ET_OFF  
> ```  
>   
> Hmmm I was wondering if you would like to use a PP-definition that has one more word in it. Maybe someday, there will be several kinds of ETs in Math. What do you think about `BOOST_MATH_DIFFERENTIATION_ET_OFF` or `...ON`?  
  
I'm fine with that. Would `BOOST_MATH_REVERSE_MODE_AD_ET_OFF` be better? or `BOOST_MATH_REVERSE_MODE_AD_USE_ET` that defaults to 1?

---

## Comment 53

> Username: ckormanyos  
> Created_at: 2025-08-25 09:16:03 UTC  
> Updated_at: 2025-08-25 09:16:40 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219475102  

> I'm fine with that. Would `BOOST_MATH_REVERSE_MODE_AD_ET_OFF` be better? or `BOOST_MATH_REVERSE_MODE_AD_USE_ET` that defaults to 1?  
  
That's a tough question. In Multiprecision, we use a templated enumeration type. In other words, one of the template parameters to the `number` type is an enumeration value that is either `et_off` or `et_on`. We let our clients decide (when defining the type) if they want ET on or OFF. And the clients can even mix code with types having ET on and/or off.  
  
Do you think it might be better to use a template parameter? It could default to either ET on or off, but that way, things might be more flexible or similar to the Multiprecision way?  
  
In multiprecision, we default to `et_on` I believe.

---

## Comment 54

> Username: demroz  
> Created_at: 2025-08-25 09:18:46 UTC  
> Updated_at: 2025-08-25 09:19:12 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219484177  

> > I'm fine with that. Would `BOOST_MATH_REVERSE_MODE_AD_ET_OFF` be better? or `BOOST_MATH_REVERSE_MODE_AD_USE_ET` that defaults to 1?  
>   
> That's a tough question. In Multiprecision, we use a templated enumeration type. In other words, one of the template parameters to the `number` type is an enumeration value that is either `et_off` or `et_on`. We let our clients decide (when defining the type) if they want ET on or OFF. And the clients can even mix code with types having ET on and/or off.  
>   
> Do you think it might be better to use a template parameter? It could default to either ET on or off, but that way, things might be more flexible or similar to the Multiprecision way?  
>   
> In multiprecision, we default to `et_on` I believe.  
  
This would require a significant restructure of the code I think. Right now the et on and et off simply decide which header file with function overloads to include.

---

## Comment 55

> Username: ckormanyos  
> Created_at: 2025-08-25 09:23:50 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219499850  

> This would require a significant restructure of the code I think. Right now the et on and et off simply decide which header file with function overloads to include.  
  
That'll work. If you ever decide to change down the road, you can probably still remain compatible by using a default parameterr value. So the simpler evolutionary step you suggest seems just fine for the moment.  
  
> `BOOST_MATH_REVERSE_MODE_AD_ET_OFF` be better? or `BOOST_MATH_REVERSE_MODE_AD_USE_ET`  
  
These seem fine from above, if you're happy with them, I'm happy with them.

---

## Comment 56

> Username: demroz  
> Created_at: 2025-08-25 10:49:47 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3219760357  

@mborland updated the docs based on your suggestions

---

## Comment 57

> Username: demroz  
> Created_at: 2025-08-25 13:16:57 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3220234539  

Is there anything else needed from my end to get this merged?

---

## Comment 58

> Username: ckormanyos  
> Created_at: 2025-08-25 13:33:57 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3220304851  

> Is there anything else needed from my end to get this merged?  
  
I don't know why it was still necessary to approve your workflow runs (CI/CD on GHA). But I just did that, after overlooking the button for a week or so. I was sure we had approved them initially, but I guess not.  
  
Let's see how it runs and shakes out on GHA. Other than that, from my side (Christopher), it's good to go.  
  
So it might be a good idea to see how the Code Coverage looks. And if we don't like it, we can add tests either prior to or any time after the merge.  
  
Cc: @jzmaddock and @mborland

---

## Comment 59

> Username: codecov[bot]  
> Created_at: 2025-08-25 13:34:59 UTC  
> Updated_at: 2025-08-26 09:38:15 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3220308741  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1302?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.10%. Comparing base ([`7535ee1`](https://app.codecov.io/gh/boostorg/math/commit/7535ee105b3e42462f9bc1879a6b8bcbbe79c22b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2c943fb`](https://app.codecov.io/gh/boostorg/math/commit/2c943fbbc45ffa781f7ce4aa4f022a10a93e7c77?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 79 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1302/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1302?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1302      +/-   ##  
===========================================  
+ Coverage    90.47%   95.10%   +4.62%       
===========================================  
  Files          191      796     +605       
  Lines        23771    67115   +43344       
===========================================  
+ Hits         21507    63830   +42323       
- Misses        2264     3285    +1021       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1302?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/sin\_pi.hpp](https://app.codecov.io/gh/boostorg/math/pull/1302?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fsin_pi.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3Npbl9waS5ocHA=) | `100.00% <ø> (+17.39%)` | :arrow_up: |  
  
... and [667 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1302/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1302?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1302?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7535ee1...2c943fb](https://app.codecov.io/gh/boostorg/math/pull/1302?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 60 [Changes requested]

> Username: mborland  
> Created_at: 2025-08-25 13:55:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1302#pullrequestreview-3151586077  

> Username: mborland  
> Created_at: 2025-08-25 13:54:42 UTC  
> Updated_at: 2025-08-25 13:55:01 UTC  
> Url: https://github.com/boostorg/math/pull/1302#discussion_r2298179905  

This needs to be put back. You can copy and paste the one that's currently on develop


---

## Comment 61

> Username: mborland  
> Created_at: 2025-08-25 13:57:18 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3220394824  

Other than the main CMakeLists.txt inadvertently being deleted I am good with this PR assuming it runs green.

---

## Comment 62

> Username: demroz  
> Created_at: 2025-08-25 14:01:50 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3220425144  

> Other than the main CMakeLists.txt inadvertently being deleted I am good with this PR assuming it runs green.  
  
strange. I added it back in

---

## Comment 63

> Username: demroz  
> Created_at: 2025-08-26 08:55:22 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3223245950  

Does it still make sense for the ET on / ET off change to be a separate PR? Its something that I have working. I guess it would be good to compartmentalize the discussion.

---

## Comment 64

> Username: mborland  
> Created_at: 2025-08-26 09:37:04 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3223414645  

> Does it still make sense for the ET on / ET off change to be a separate PR? It's something that I have working. I guess it would be good to compartmentalize the discussion.  
  
I think so. I'll merge this in and then you can do more targeted feature branches.

---

## Comment 65

> Username: ckormanyos  
> Created_at: 2025-08-26 17:03:58 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3225019995  

> I'll merge this in and then you can do more targeted feature branches.  
  
Thanks @demroz nice work. I'm excited to see how this area of Math evolves.  
  
Thanks also Matt and John.

---

## Comment 66

> Username: ckormanyos  
> Created_at: 2025-08-26 17:05:39 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3225024566  

Hi @demroz is it appropriate or useful to add a phrase or extension of [this sentence](https://github.com/boostorg/math?tab=readme-ov-file#optimization) in the main docs?

---

## Comment 67

> Username: demroz  
> Created_at: 2025-08-27 02:39:04 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3226547096  

I'm sorry I dont understand what you mean exactly. Are you asking if its appropriate to add a sentence about cost function minimization to the autodiff docs? Or adding a setance about autodiff to the readme file?

---

## Comment 68

> Username: xuruilong100  
> Created_at: 2025-12-19 02:01:40 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3673133438  

Hi, I find that in Boost 1.90, `autodiff.hpp` and `autodiff_reverse.hpp` share same macro:  
  
```cpp  
#ifndef BOOST_MATH_DIFFERENTIATION_AUTODIFF_HPP  
#define BOOST_MATH_DIFFERENTIATION_AUTODIFF_HPP  
```  
  
So, I can't include them in same file. Is it a bug? Or, is it a feature?  
  
BTW, there is a typo about your name in `autodiff_reverse.hpp`, users can't find you in github by searching `Maksym Zhelyenzyakov`.  
  
```cpp  
//           Copyright Maksym Zhelyenzyakov 2025-2026.  
// Distributed under the Boost Software License, Version 1.0.  
//      (See accompanying file LICENSE_1_0.txt or copy at  
//           https://www.boost.org/LICENSE_1_0.txt)  
```

---

## Comment 69

> Username: demroz  
> Created_at: 2025-12-19 07:33:17 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3673930180  

@xuruilong100 Thanks. The macro being the same between the two files is indeed a bug. I'll update it. And thanks for pointing out the misspelling. I guess my name is so complicated even i can't spell it right

---

## Comment 70

> Username: xuruilong100  
> Created_at: 2025-12-22 08:09:23 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3680951999  

Other two questions:  
1. Is `autodiff_reverse` thread safe? If it isn't safe, what should I do to make it thread safe?  
2. Is there a performance test with other libraries? For example CppAD and [autodiff](https://github.com/autodiff/autodiff)

---

## Comment 71

> Username: demroz  
> Created_at: 2025-12-22 08:16:05 UTC  
> Updated_at: 2025-12-22 08:27:54 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3680970780  

> Other two questions:  
>   
> 1. Is `autodiff_reverse` thread safe? If it isn't safe, what should I do to make it thread safe?  
> 2. Is there a performance test with other libraries? For example CppAD and [autodiff](https://github.com/autodiff/autodiff)  
  
1. the ```autodiff_reverse``` tape is marked as ```BOOST_MATH_THREAD_LOCAL```. So one tape per thread  
2. Its on my to-do list. I'm not sure about CppAD, but as far as I'm aware autodiff doesn't use a tape or ET. That alone should make ```autodiff_reverse``` faster, but don't quote me on that.  
  
If speed is an issue, you can try messing with ```BOOST_MATH_BUFFER_SIZE```. Its currently set to ```65536``` The bottleneck currently is tape memory allocation. If your calculation is very short ```4096``` may be a more reasonable value

---

## Comment 72

> Username: xuruilong100  
> Created_at: 2025-12-22 13:20:15 UTC  
> Url: https://github.com/boostorg/math/pull/1302#issuecomment-3682074363  

@demroz Thank you, good suggestion.

---
