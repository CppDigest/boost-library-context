# #754 - Unexpected instantiation of `chi_squared_distribution` in C++17 [Closed]

> Username: jamesfolberth  
> Created at: 2022-02-02 05:41:16 UTC  
> Updated at: 2022-02-12 19:09:40 UTC  
> Closed at: 2022-02-12 19:09:40 UTC  
> Url: https://github.com/boostorg/math/issues/754  

The following is valid in C++17, AFAIK.  
```c++  
#include <iostream>  
#include <boost/math/distributions/chi_squared.hpp>  
  
int main()  
{  
    int dof=2;  
    boost::math::chi_squared_distribution chiSqi(dof);  
    boost::math::chi_squared_distribution<double> chiSq(dof);  
  
    std::cout << "chiSqi cdf: " << boost::math::cdf(chiSqi,3.1415) << std::endl;  
    std::cout << "chiSq cdf: " << boost::math::cdf(chiSq,3.1415) << std::endl;  
  
    return 0;  
}  
```  
  
The above code produces the following *unexpected* output under GCC and clang with `-std=c++17`.  
```  
chiSqi cdf: 0  
chiSq cdf: 0.792111  
```  
  
In C++17, we may be able to omit the class template arguments ([CTAD](https://en.cppreference.com/w/cpp/language/class_template_argument_deduction)) in favor of allowing the compiler to deduce them.  
`chi_squared_distribution` is parameterized by a `RealType` template argument, defaulting to a `double`.  
In the first case, where I don't explicitly provide the template argument, the compiler deduces the template parameter `RealType` to be an `int` based on the argument passed to the ctor.  
  
I think it is unexpected/bad to allow instantiating a `chi_squared_distribution` with an *integral* `RealType`.  
The ctor should happily accept an integral degrees of freedom, but the type used for the calculations shouldn't be integral.  
Perhaps `chi_squared_distribution` could use `enable_if` to check that `RealType` `is_floating_point`?  
This would force the user to explicitly provide the class template argument in cases where CTAD chooses the "wrong" type.  
  
I'd be happy to submit a PR. Is the proposed solution okay? Is there a better solution?  
  
Additionally, there may be distributions other than `chi_squared_distribution` that are affected by the same pattern.  
  
os: Arch Linux, Linux 5.16.3  
boost version: 1.78.0-1 (from Arch repo)  
compiler: GCC 11.1.0, clang 13.0.0-4

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-02-02 15:25:15 UTC  
> Updated at: 2022-02-02 15:25:27 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028053789  

You want to:  
  
```  
static_assert(!std::is_integral<RealType>::value, "Chi Squared distribution must be templated on a floating point type");  
```  
  
?

---

## Comment 2

> Username: jamesfolberth  
> Created at: 2022-02-02 15:40:50 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028070419  

Yup, that's what I was thinking. I'd change `!std::is_integral` to `std::is_floating_point` to disallow `std::complex<T>`.

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-02-02 16:31:59 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028123228  

Unfortunately we can't use `std::is_floating_point` because that would break compatibility with boost.multiprecision. Disallowing std::complex is probably a good idea.

---

## Comment 4

> Username: jamesfolberth  
> Created at: 2022-02-02 16:44:39 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028135188  

Ah, I didn't know that. Thanks for the correction! I'll put together a PR for this (probably over the weekend). I'll also poke through other distributions to see if they're open to the same behavior.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-02-02 17:09:32 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028159476  

Looks like we need to be using deduction guides, with promotion of integer types to double, ie use `boost::math::tools::promote_args<constructor arguments..>::type` as the trailing return type.  
  
There would be quite a bit of #if logic and general testing required I suspect :(

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-02-02 19:30:27 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028284975  

OK, I don't really have the time to go through all of these right now, but this branch: https://github.com/boostorg/math/tree/issue754 demonstrates tests and changes required for the normal_distribution by way of an example.  
  
Diff: https://github.com/boostorg/math/compare/issue754  
  
@NAThompson do we need this for all the integrators and possibly other stuff as well?

---

## Comment 7

> Username: NAThompson  
> Created at: 2022-02-02 19:54:39 UTC  
> Updated at: 2022-02-02 19:59:15 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1028303591  

> @NAThompson do we need this for all the integrators and possibly other stuff as well?  
  
You can see we have a static_assert in tanh_sinh:  
  
https://github.com/boostorg/math/blob/a36f6a54a96006c3515ebf0acf17d180322042f2/include/boost/math/quadrature/tanh_sinh.hpp#L71  
  
and exp_sinh:  
  
https://github.com/boostorg/math/blob/a36f6a54a96006c3515ebf0acf17d180322042f2/include/boost/math/quadrature/exp_sinh.hpp#L47  
  
and gauss:  
  
https://github.com/boostorg/math/blob/a36f6a54a96006c3515ebf0acf17d180322042f2/include/boost/math/quadrature/gauss.hpp#L1182  
  
and Gauss-Kronrod:  
  
https://github.com/boostorg/math/blob/a36f6a54a96006c3515ebf0acf17d180322042f2/include/boost/math/quadrature/gauss_kronrod.hpp#L1874  
  
I haven't gone through all of the integrators, but I expect a bit of effort.

---

## Comment 8

> Username: jamesfolberth  
> Created at: 2022-02-04 05:29:37 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1029646261  

I like the deduction guide approach, but as @mborland notes in #755 it seems to require C++17 support. So do you guys want to go with `enable_if`? Or perhaps `enable_if` if < C++17, deduction guide otherwise (via #if) so the <C++17 branch can be easily removed in the future? Or per @NAThompson's original comment, just do a `static_assert` as is done a lot elsewhere?  
  
As I'm not familiar with the guts of boost::math, I'm just trying to get a feel for what you guys would like.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-02-04 09:00:17 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1029776280  

Is this even an issue prior to C++17 given that deduced class template arguments are a C++17 feature?  
  
OK, I guess someone could deliberately try to instantiate `normal_distribution<int>` but that would be kind of silly?  
  
Just thinking out loud here....

---

## Comment 10

> Username: jamesfolberth  
> Created at: 2022-02-04 15:07:52 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1030072894  

Oh yeah, you're absolutely right: it's *not* an issue prior to C++17. Slickest solution is your deduction guide with the proper #if, and a static_assert for the silly case. I'll work on a PR this weekend.

---

## Comment 11

> Username: mborland  
> Created at: 2022-02-04 19:03:01 UTC  
> Url: https://github.com/boostorg/math/issues/754#issuecomment-1030267899  

Looks like deduction guides are the way to go. Old versions of clang with SFINAE [don't seem to work quite right.](https://github.com/boostorg/math/runs/5062708637?check_suite_focus=true#step:18:453)
