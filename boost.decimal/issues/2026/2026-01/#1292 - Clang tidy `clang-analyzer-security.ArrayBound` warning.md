# #1292 - Clang tidy `clang-analyzer-security.ArrayBound` warning [Closed]

> Username: edubart  
> Created at: 2026-01-09 17:24:47 UTC  
> Updated at: 2026-01-09 17:54:04 UTC  
> Closed at: 2026-01-09 17:54:04 UTC  
> Url: https://github.com/boostorg/decimal/issues/1292  

I am using `clang-tidy` v21.1.6 to do static analysis of code using this library, but I am getting `clang-analyzer-security.ArrayBound` warnings. I am not sure if this is a false positive. Here is a small test case:  
  
```c++  
#include <cstdint>  
#include <boost/decimal/decimal64_t.hpp>  
  
[[nodiscard]] static constexpr auto int64_to_rounded_float(std::int64_t coefficient, int exponent) -> boost::decimal::decimal64_t {  
    return boost::decimal::decimal64_t{coefficient, exponent};  
}  
```  
  
Using `clang-tidy` with:  
```  
$ clang-tidy test.cpp -- -std=c++23 -I./third-party/decimal/include  
```  
  
Outputs:  
```  
3 warnings generated.  
./third-party/decimal/include/boost/decimal/detail/power_tables.hpp:217:27: warning: Out of bound access to memory after the end of 'powers_of_10' [clang-analyzer-security.ArrayBound]  
  217 |     return static_cast<T>(impl::powers_of_10[static_cast<std::size_t>(n)]);  
      |                           ^  
/home/bart/projects/app/test.cpp:6:12: note: Calling constructor for 'decimal64_t'  
    6 |     return boost::decimal::decimal64_t{coefficient, exponent};  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:837:133: note: 'coeff' is >= 0  
  837 | constexpr decimal64_t::decimal64_t(const T1 coeff, const T2 exp) noexcept : decimal64_t(detail::make_positive_unsigned(coeff), exp, coeff < 0) {}  
      |                                                                                                                                     ^~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:837:77: note: Calling constructor for 'decimal64_t'  
  837 | constexpr decimal64_t::decimal64_t(const T1 coeff, const T2 exp) noexcept : decimal64_t(detail::make_positive_unsigned(coeff), exp, coeff < 0) {}  
      |                                                                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:673:13: note: 'is_negative' is false  
  673 |     bits_ = is_negative ? detail::d64_sign_mask : UINT64_C(0);  
      |             ^~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:673:13: note: '?' condition is false  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:678:9: note: Assuming 'coeff' is <= 'd64_max_significand_value'  
  678 |     if (coeff > detail::d64_max_significand_value || biased_exp < -(detail::precision_v<decimal64_t> - 1))  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:678:9: note: Left side of '||' is false  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:678:54: note: Assuming the condition is false  
  678 |     if (coeff > detail::d64_max_significand_value || biased_exp < -(detail::precision_v<decimal64_t> - 1))  
      |                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:678:5: note: Taking false branch  
  678 |     if (coeff > detail::d64_max_significand_value || biased_exp < -(detail::precision_v<decimal64_t> - 1))  
      |     ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:686:9: note: Assuming 'reduced_coeff' is not equal to 0  
  686 |     if (reduced_coeff == 0U)  
      |         ^~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:686:5: note: Taking false branch  
  686 |     if (reduced_coeff == 0U)  
      |     ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:692:9: note: Assuming 'reduced_coeff' is <= 'd64_biggest_no_combination_significand'  
  692 |     if (reduced_coeff <= detail::d64_biggest_no_combination_significand)  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:692:5: note: Taking true branch  
  692 |     if (reduced_coeff <= detail::d64_biggest_no_combination_significand)  
      |     ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:706:30: note: Assuming 'biased_exp' is < 0  
  706 |     if (BOOST_DECIMAL_LIKELY(biased_exp >= 0 && biased_exp <= static_cast<int>(detail::d64_max_biased_exponent)))  
      |                              ^  
./third-party/decimal/include/boost/decimal/detail/config.hpp:208:52: note: expanded from macro 'BOOST_DECIMAL_LIKELY'  
  208 | #  define BOOST_DECIMAL_LIKELY(x) __builtin_expect(x, 1)  
      |                                                    ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:706:46: note: Left side of '&&' is false  
  706 |     if (BOOST_DECIMAL_LIKELY(biased_exp >= 0 && biased_exp <= static_cast<int>(detail::d64_max_biased_exponent)))  
      |                                              ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:706:5: note: Taking false branch  
  706 |     if (BOOST_DECIMAL_LIKELY(biased_exp >= 0 && biased_exp <= static_cast<int>(detail::d64_max_biased_exponent)))  
      |     ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:722:9: note: Taking true branch  
  722 |         if (coeff_digits == -1)  
      |         ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:729:13: note: 'biased_exp' is < 0  
  729 |         if (biased_exp < 0 && coeff_digits == 1)  
      |             ^~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:729:13: note: Left side of '&&' is true  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:729:31: note: 'coeff_digits' is not equal to 1  
  729 |         if (biased_exp < 0 && coeff_digits == 1)  
      |                               ^~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:729:9: note: Taking false branch  
  729 |         if (biased_exp < 0 && coeff_digits == 1)  
      |         ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:781:18: note: Assuming 'digit_delta' is > 0  
  781 |         else if (digit_delta > 0 && coeff_digits + digit_delta <= detail::precision_v<decimal64_t>)  
      |                  ^~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:781:18: note: Left side of '&&' is true  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:781:37: note: Assuming the condition is true  
  781 |         else if (digit_delta > 0 && coeff_digits + digit_delta <= detail::precision_v<decimal64_t>)  
      |                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:781:14: note: Taking true branch  
  781 |         else if (digit_delta > 0 && coeff_digits + digit_delta <= detail::precision_v<decimal64_t>)  
      |              ^  
./third-party/decimal/include/boost/decimal/decimal64_t.hpp:784:30: note: Calling 'pow10<unsigned long>'  
  784 |             reduced_coeff *= detail::pow10(static_cast<significand_type>(digit_delta));  
      |                              ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./third-party/decimal/include/boost/decimal/detail/power_tables.hpp:217:27: note: Access of 'powers_of_10' at an overflowing index, while it holds only 20 'unsigned long' elements  
  217 |     return static_cast<T>(impl::powers_of_10[static_cast<std::size_t>(n)]);  
      |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Let me know if this a library error.

---

## Comment 1

> Username: mborland  
> Created at: 2026-01-09 17:29:27 UTC  
> Url: https://github.com/boostorg/decimal/issues/1292#issuecomment-3729901930  

What values of coefficient and exponent trigger this?

---

## Comment 2

> Username: mborland  
> Created at: 2026-01-09 17:39:17 UTC  
> Url: https://github.com/boostorg/decimal/issues/1292#issuecomment-3729932245  

Actually it doesn't matter. Both `coeff_digits` and `digit_delta` are greater than 0 and less than precision which is 16. A number between 0 and 16 can't overflow an array of size 20

---

## Comment 3

> Username: edubart  
> Created at: 2026-01-09 17:42:52 UTC  
> Updated at: 2026-01-09 17:43:44 UTC  
> Url: https://github.com/boostorg/decimal/issues/1292#issuecomment-3729943081  

Adding `assert(biased_exp < 20)` just before `detail::pow10(static_cast<significand_type>(biased_exp))` seems to silence the analyzer. Looks like the analyzer is just missing more context. I don't know if is a goal for the library to support `clang-tidy`, if so maybe it's worth adding some kind of assert.

---

## Comment 4

> Username: mborland  
> Created at: 2026-01-09 17:54:04 UTC  
> Url: https://github.com/boostorg/decimal/issues/1292#issuecomment-3729976503  

I find `clang-tidy` signal to noise ratio too low to want to support it fully.
