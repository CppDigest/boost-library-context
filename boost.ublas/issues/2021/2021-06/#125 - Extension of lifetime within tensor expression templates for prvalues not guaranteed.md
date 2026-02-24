# #125 - Extension of lifetime within tensor expression templates for prvalues not guaranteed. [Open]

> Username: bassoy  
> Created at: 2021-06-04 22:16:47 UTC  
> Updated at: 2022-02-09 14:02:41 UTC  
> Url: https://github.com/boostorg/ublas/issues/125  

The current expression template implementation binds `rvalue` expressions to const references.  
In such cases the lifetime of designated temporary objects are automatically extended due to `prvalue` to `xvalue` expression conversion, see also [temporary materialization](https://en.cppreference.com/w/cpp/language/implicit_conversion#Temporary_materialization) and [value categorization](https://en.cppreference.com/w/cpp/language/value_category).   
  
In case of `prvalue` expressions such as `42` or `42+13`, it seems that such a lifetime extension dos not work such that tensor expressions are **not** correctly evaluated although tensor expression objects are never copied. We suspect that this bug results from the mandatory [copy elision](https://en.cppreference.com/w/cpp/language/copy_elision) rules as the `prvalue` is passed without materialization.  
  
Possible fixes are  
- to use `value_type` instead of `const_reference` in expressions such as in https://github.com/boostorg/ublas/blob/db29cdf76cdd4c3732164c99e9a22e9e02bf994f/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp#L291  
- or to distinguish between tensor element and tensor types, see also this [example](https://godbolt.org/z/o6bez15oP)

---

## Comment 1

> Username: Neel-Shah-29  
> Created at: 2022-02-07 20:32:43 UTC  
> Url: https://github.com/boostorg/ublas/issues/125#issuecomment-1031893300  

Hello! If there is no ongoing work done on this issue by anyone, then can i try to fix this issue. Kindly assign it to me if no one is working on it.
