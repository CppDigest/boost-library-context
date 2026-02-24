# #156 - Multiprecision floating point types don't register as such with std [Closed]

> Username: kshegunov  
> Created at: 2019-09-01 19:44:56 UTC  
> Updated at: 2019-09-03 08:02:21 UTC  
> Closed at: 2019-09-02 07:40:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/156  

Hello,  
The following typedef:  
```  
using namespace boost::multiprecision;  
typedef number<backends::cpp_bin_float<2048, backends::digit_base_2, void, boost::int32_t, -32766, 32767>, et_off> floatx;  
```  
  
Doesn't register as a floating point type with std, i.e. `std::is_floating_point<floatx>::value` is `false`. It may not be truly easy to implement, but I do expect them to define the `std::is_floating_point< ... >::value` constant as true, do I miss something here?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2019-09-01 21:42:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/156#issuecomment-526955189  

I believe that std::is_floating_point<T>::value is only true if T is of built-in type float, double or long double. Apparantly, this is not specified for user-defined floating-point types, as the type cpp_bin_float is a user-defined type.  
  
Best regards, Chris

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-09-02 07:40:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/156#issuecomment-527038718  

Chris is correct, type_traits tells you about what kind of type it is (it's a class type and not a fundamental type).  If you want to know about it's behaviour, use std::numeric_limits.

---

## Comment 3

> Username: kshegunov  
> Created at: 2019-09-03 08:02:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/156#issuecomment-527350710  

Fair enough.  
Thanks for looking into it!
