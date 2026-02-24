# #1020 - promotions [Closed]

> Username: gpeterhoff  
> Created at: 2023-08-23 18:01:35 UTC  
> Updated at: 2023-08-23 18:20:06 UTC  
> Closed at: 2023-08-23 18:20:06 UTC  
> Url: https://github.com/boostorg/math/issues/1020  

Hello,  
boost/math/toolts/promotion.hpp is corrupt. Unfortunately, I can't say whether some promotions should actually work like this or whether there is an error.  
Reasons:  
- promotions are implemented manually instead of using std::type_traits. This is highly confusing and error-prone.  
- No variadic templates are used but (incorrectly) reproduced manually, see 1.).  
  
boost::math needs C++14 anyway - so I don't understand why legacy C++98/03 is still used.  
  
In order to be able to better illustrate the problems, the behavior of the std::math functions is briefly shown again:  
- one argument, e.g. std::sin:  
   std::sin(Float) -> Float,  
   std::sin(Integral) -> double  
- multiple arguments + with C++14 the specializations foo(Float, int) have been removed, e.g. std::pow:  
   std::pow(Float, Float) -> Float  
   std::pow(Integral, Integral) -> double  
   std::pow(Float1, Float2) -> std::common_type_t<Float1, Float2>  
   std::pow(Float, Integral) + std::pow(Integral, Float) -> std::common_type_t<Float, double>  
  
In boost/math/toolts/promotion.hpp there are 3 promotions:  
- promote_arg  
- promote_args  
- promote_args_2  
  
fundamental problem  
- std::bfloat16_t is not supported  
  
In detail  
- promote_arg  
   Works as it should, but is unnecessarily bloated with manual specializations.  
  
- promote_args  
   By 1.) neither std::float16_t nor std::bfloat16_t are fully supported.  
  
- promote_args_2  
   Here I'm not sure what the purpose is as the implementations for std::float128_t/std::float64_t/std::float32_t/std::float16_t (std::bfloat16_t is missing as said) and long double/double/float differ. In addition, it is highly confusing/prone to errors.  
   Mistake:  
     promote_args_2<int, float>/promote_args_2<float, int> -> double (default behavior)  
     promote_args_2<int, std::float32_t>/promote_args_2<std::float32_t, int> -> std::float32_t  
  
   There are 2 possible solutions:  
     a) promote_args_2<Type1, Type2> behaves exactly like promote_args<Type1, Type2> -> would be superfluous  
     b) If actually needed:  
        promote_args_2<Float1, Float2> -> std::common_type_t<Float1, Float2>  
        promote_args_2<Integral, Integral> -> double  
        promote_args_2<Float, Integral> -> Float  
        promote_args_2<Integral, Float> -> Float  
  
I've implemented a few cases [promotion.hpp.txt](https://github.com/boostorg/math/files/12421960/promotion.hpp.txt)  
  
- v1  
   correct promote_arg(s)  
   promote_args_2 like a)  
- v2  
   correct promote_arg(s)  
   promote_args_2 like b)  
- v3  
   correct promote_arg(s)  
   promote_args_2 like a)   
   conversion std::floatN_t -> boost::floatN_t  
   This might be necessary for compatibility reasons.  
- v4  
   correct promote_arg(s)  
   promote_args_2 like a)  
   conversion boost::floatN_t -> std::floatN_t  
   This has the advantage that the corresponding math functions are (partially) constexpr, which of course is not the case with __float128 (C library).  
- v5  
   like v4 but promote_args_2 like b)  
  
Of course, I think v4/v5 makes the most sense.  
  
These measures can probably remove promote_args_permissive as well.  
In general, boost/math/special_functions already included in C++14 can be removed.  
  
1.)  
template <class T1, class T2=float, class T3=float, class T4=float, class T5=float, class T6=float> struct promote_args  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-08-23 18:20:06 UTC  
> Url: https://github.com/boostorg/math/issues/1020#issuecomment-1690426244  

There is no specialization for `std::bfloat16_t` because what is the reasonable conversion? I just ran the tests from `test_beta_dist` with `std::bfloat16_t` on GCC-13, and they run but are inaccurate as expected. There is discussion at the top of the [PR adding support](https://github.com/boostorg/math/pull/978) as to our design decisions. PRs with your suggestions are always welcome.
