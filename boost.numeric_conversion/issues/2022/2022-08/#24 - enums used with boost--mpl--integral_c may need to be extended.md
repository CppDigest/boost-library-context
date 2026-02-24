# #24 - enums used with boost::mpl::integral_c may need to be extended. [Closed]

> Username: dabrahams  
> Created at: 2022-08-09 04:43:34 UTC  
> Updated at: 2022-11-16 20:43:32 UTC  
> Closed at: 2022-11-16 20:43:31 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24  

[This issue](https://github.com/boostorg/mpl/issues/69#issuecomment-1208905334) suggests that the enums being used with mpl::integral_c need two additional cases.  The problem was detected by the newest Clang in trunk.

---

## Comment 1

> Username: dabrahams  
> Created at: 2022-08-10 02:20:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24#issuecomment-1210074220  

Actually the easiest workaround is to add an underlying type (e.g. `int`) for each of these enums.  
  
```cpp  
enum example : int { zero };  
constexpr example x{static_cast<example>(-1)};  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2022-11-16 01:00:53 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24#issuecomment-1316113752  

Can't add `: int` in C++03 libraries though.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-16 08:33:32 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24#issuecomment-1316596703  

Something like this  
```  
enum int_float_mixture_enum  
{  
    // for mpl::integral_c<int_float_mixture_enum, integral_to_integral>  
    int_float_mixture_enum_unused_first = -1,  
  
    integral_to_integral,  
    integral_to_float,  
    float_to_integral,  
    float_to_float,  
  
    // for mpl::integral_c<int_float_mixture_enum, float_to_float>  
    int_float_mixture_enum_unused_last  
};  
```  
works, but then `switch` statements start generating "not all enumeration values handled in a switch" warnings. :-/

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-16 08:44:53 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24#issuecomment-1316611295  

[Switching to boost::integral_constant](https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a) seems to work and is the least intrusive fix.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-11-16 20:43:31 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/24#issuecomment-1317644879  

Applied https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a to develop.
