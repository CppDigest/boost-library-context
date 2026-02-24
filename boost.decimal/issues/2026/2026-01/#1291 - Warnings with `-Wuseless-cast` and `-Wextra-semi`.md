# #1291 - Warnings with `-Wuseless-cast` and `-Wextra-semi` [Closed]

> Username: edubart  
> Created at: 2026-01-09 15:13:30 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-10 00:43:15 UTC  
> Url: https://github.com/boostorg/decimal/issues/1291  

I was trying the library, I usually enable a lot of warnings, and got these warnings:  
  
```  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/next.hpp:19,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal32.hpp:41,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal32_t.hpp:8,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:27,  
                 from ../src/float.hpp:10,  
                 from ../src/fixed-decimal.hpp:15,  
                 from test-fixed-decimal.cpp:13:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp: In function ‘constexpr int boost::decimal::ilogb(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:31:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   31 |         result = static_cast<int>(FP_ILOGB0);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:36:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   36 |         result = static_cast<int>(INT_MAX);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:40:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   40 |         result = static_cast<int>(FP_ILOGBNAN);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal_fast128_t.hpp:8,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:32:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp: At global scope:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:810:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
  810 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1024:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1024 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1033:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1033 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1125:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1125 | };  
      |  ^  
      |  -  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp:13,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/charconv.hpp:22,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/literals.hpp:17,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/numbers.hpp:9,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/acosh.hpp:12,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:12,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:33:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memcpy(char*, const char*, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:88:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
   88 |         return static_cast<char*>(impl::memcpy_impl(dest, src, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memset(char*, int, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:112:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
  112 |         return static_cast<char*>(impl::memset_impl(dest, ch, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memmove(char*, const char*, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:124:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
  124 |         return static_cast<char*>(impl::memmove_impl(dest, src, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp: In function ‘constexpr char* boost::decimal::detail::decompose32(uint32_t, char*)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp:141:66: warning: useless cast to type ‘std::size_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
  141 |         boost::decimal::detail::memcpy(buffer + i, radix_table + static_cast<std::size_t>(y >> 57) * 2, 2);  
      |                                                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:16:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/cos.hpp: In function ‘constexpr T boost::decimal::detail::cos_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/cos.hpp:68:28: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   68 |             const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:18:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/ellint_impl.hpp: In function ‘constexpr void boost::decimal::detail::ellint_detail::elliptic_series::agm(T, T, T&, T&, T*, T*) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/ellint_impl.hpp:138:12: warning: useless cast to type ‘uint32_t’ {aka ‘unsigned int’} [-Wuseless-cast]  
  138 |       p2 = static_cast<std::uint32_t>(p2 << 1U);  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:12:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/atan.hpp: In function ‘constexpr T boost::decimal::detail::atan_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/atan.hpp:87:65: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   87 |                 constexpr T my_pi_over_six { numbers::pi_v<T> / static_cast<int>(INT8_C(6)) };  
      |                                                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:15:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp: In function ‘constexpr T boost::decimal::detail::sin_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp:52:16: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   52 |         if(x > static_cast<int>(INT8_C(0)))  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp:68:28: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   68 |             const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:17:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/tan.hpp: In function ‘constexpr T boost::decimal::detail::tan_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/tan.hpp:73:24: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   73 |         const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:20:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp: In function ‘constexpr std::enable_if_t<((bool)is_decimal_floating_point_v<Decimal>), Decimal> boost::decimal::detail::pow_2_impl(int)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:89:26: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
   89 |             result = T { static_cast<std::uint64_t>(UINT64_C(1) << e2), 0 };  
      |                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:100:29: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
  100 |         const auto e2_neg = static_cast<unsigned>(static_cast<unsigned>(~e2) + 1U);  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:104:32: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
  104 |             const T local_p2 { static_cast<std::uint64_t>(UINT64_C(1) << e2_neg), 0 };  
      |                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp: In function ‘constexpr T boost::decimal::detail::exp_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp:80:35: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
   80 |                     result *= T { static_cast<std::uint64_t>(UINT64_C(1) << static_cast<unsigned>(nf2)), 0 };  
      |                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp2.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:21:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/pow.hpp: In function ‘constexpr T boost::decimal::pow(T, IntegralType) requires (is_decimal_floating_point_v<T>) && (is_integral_v<IntegralType>)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/pow.hpp:117:29: warning: useless cast to type ‘int’ [-Wuseless-cast]  
  117 |             result = T { 1, static_cast<int>(log10_val * static_cast<int>(p)) };  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:30:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp: In function ‘constexpr T boost::decimal::detail::frexp_impl(T, int*) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp:63:19: warning: useless cast to type ‘int32_t’ {aka ‘int’} [-Wuseless-cast]  
   63 |                   static_cast<std::int32_t>  
      |                   ^~~~~~~~~~~~~~~~~~~~~~~~~  
   64 |                   (  
      |                   ~  
   65 |                       static_cast<std::int32_t>(ilogb(result_frexp) - detail::bias) * INT32_C(1000)  
      |                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   66 |                   )  
      |                   ~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp:61:13: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   61 |             static_cast<int>  
      |             ^~~~~~~~~~~~~~~~  
   62 |             (  
      |             ~  
   63 |                   static_cast<std::int32_t>  
      |                   ~~~~~~~~~~~~~~~~~~~~~~~~~  
   64 |                   (  
      |                   ~  
   65 |                       static_cast<std::int32_t>(ilogb(result_frexp) - detail::bias) * INT32_C(1000)  
      |                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   66 |                   )  
      |                   ~  
   67 |                 / INT32_C(301)  
      |                 ~~~~~~~~~~~~~~  
   68 |             );  
      |             ~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/riemann_zeta.hpp:10,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:50:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/riemann_zeta_impl.hpp: In function ‘constexpr int boost::decimal::detail::riemann_zeta_decimal_order(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/riemann_zeta_impl.hpp:357:12: warning: useless cast to type ‘int’ [-Wuseless-cast]  
  357 |     return static_cast<int>(n + order_bias);  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/next.hpp:19,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal32.hpp:41,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal32_t.hpp:8,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:27,  
                 from ../src/float.hpp:10,  
                 from ../src/fixed-decimal.hpp:15,  
                 from ../src/account.hpp:13,  
                 from ../src/exchange.hpp:13,  
                 from test-orderbook.cpp:12:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp: In function ‘constexpr int boost::decimal::ilogb(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:31:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   31 |         result = static_cast<int>(FP_ILOGB0);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:36:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   36 |         result = static_cast<int>(INT_MAX);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ilogb.hpp:40:18: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   40 |         result = static_cast<int>(FP_ILOGBNAN);  
      |                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal_fast128_t.hpp:8,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:32:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp: At global scope:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:810:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
  810 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1024:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1024 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1033:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1033 | };  
      |  ^  
      |  -  
../v5.2.0/decimal-5.2.0/include/boost/decimal/decimal128_fast.hpp:1125:2: warning: extra ‘;’ outside of a function [-Wextra-semi]  
 1125 | };  
      |  ^  
      |  -  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp:13,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/charconv.hpp:22,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/literals.hpp:17,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/numbers.hpp:9,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/acosh.hpp:12,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:12,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal.hpp:33:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memcpy(char*, const char*, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:88:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
   88 |         return static_cast<char*>(impl::memcpy_impl(dest, src, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memset(char*, int, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:112:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
  112 |         return static_cast<char*>(impl::memset_impl(dest, ch, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp: In function ‘constexpr char* boost::decimal::detail::memmove(char*, const char*, std::size_t)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/memcpy.hpp:124:16: warning: useless cast to type ‘char*’ [-Wuseless-cast]  
  124 |         return static_cast<char*>(impl::memmove_impl(dest, src, count));  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp: In function ‘constexpr char* boost::decimal::detail::decompose32(uint32_t, char*)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/to_chars_integer_impl.hpp:141:66: warning: useless cast to type ‘std::size_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
  141 |         boost::decimal::detail::memcpy(buffer + i, radix_table + static_cast<std::size_t>(y >> 57) * 2, 2);  
      |                                                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:16:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/cos.hpp: In function ‘constexpr T boost::decimal::detail::cos_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/cos.hpp:68:28: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   68 |             const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:18:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/ellint_impl.hpp: In function ‘constexpr void boost::decimal::detail::ellint_detail::elliptic_series::agm(T, T, T&, T&, T*, T*) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/ellint_impl.hpp:138:12: warning: useless cast to type ‘uint32_t’ {aka ‘unsigned int’} [-Wuseless-cast]  
  138 |       p2 = static_cast<std::uint32_t>(p2 << 1U);  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:12:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/atan.hpp: In function ‘constexpr T boost::decimal::detail::atan_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/atan.hpp:87:65: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   87 |                 constexpr T my_pi_over_six { numbers::pi_v<T> / static_cast<int>(INT8_C(6)) };  
      |                                                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:15:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp: In function ‘constexpr T boost::decimal::detail::sin_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp:52:16: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   52 |         if(x > static_cast<int>(INT8_C(0)))  
      |                ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/sin.hpp:68:28: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   68 |             const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/ellint_1.hpp:17:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/tan.hpp: In function ‘constexpr T boost::decimal::detail::tan_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/tan.hpp:73:24: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
   73 |         const auto n = static_cast<unsigned>(k % static_cast<unsigned>(UINT8_C(4)));  
      |                        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:20:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp: In function ‘constexpr std::enable_if_t<((bool)is_decimal_floating_point_v<Decimal>), Decimal> boost::decimal::detail::pow_2_impl(int)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:89:26: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
   89 |             result = T { static_cast<std::uint64_t>(UINT64_C(1) << e2), 0 };  
      |                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:100:29: warning: useless cast to type ‘unsigned int’ [-Wuseless-cast]  
  100 |         const auto e2_neg = static_cast<unsigned>(static_cast<unsigned>(~e2) + 1U);  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/pow_impl.hpp:104:32: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
  104 |             const T local_p2 { static_cast<std::uint64_t>(UINT64_C(1) << e2_neg), 0 };  
      |                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp: In function ‘constexpr T boost::decimal::detail::exp_impl(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp.hpp:80:35: warning: useless cast to type ‘uint64_t’ {aka ‘long unsigned int’} [-Wuseless-cast]  
   80 |                     result *= T { static_cast<std::uint64_t>(UINT64_C(1) << static_cast<unsigned>(nf2)), 0 };  
      |                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/exp2.hpp:11,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:21:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/pow.hpp: In function ‘constexpr T boost::decimal::pow(T, IntegralType) requires (is_decimal_floating_point_v<T>) && (is_integral_v<IntegralType>)’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/pow.hpp:117:29: warning: useless cast to type ‘int’ [-Wuseless-cast]  
  117 |             result = T { 1, static_cast<int>(log10_val * static_cast<int>(p)) };  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:30:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp: In function ‘constexpr T boost::decimal::detail::frexp_impl(T, int*) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp:63:19: warning: useless cast to type ‘int32_t’ {aka ‘int’} [-Wuseless-cast]  
   63 |                   static_cast<std::int32_t>  
      |                   ^~~~~~~~~~~~~~~~~~~~~~~~~  
   64 |                   (  
      |                   ~  
   65 |                       static_cast<std::int32_t>(ilogb(result_frexp) - detail::bias) * INT32_C(1000)  
      |                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   66 |                   )  
      |                   ~  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/frexp.hpp:61:13: warning: useless cast to type ‘int’ [-Wuseless-cast]  
   61 |             static_cast<int>  
      |             ^~~~~~~~~~~~~~~~  
   62 |             (  
      |             ~  
   63 |                   static_cast<std::int32_t>  
      |                   ~~~~~~~~~~~~~~~~~~~~~~~~~  
   64 |                   (  
      |                   ~  
   65 |                       static_cast<std::int32_t>(ilogb(result_frexp) - detail::bias) * INT32_C(1000)  
      |                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   66 |                   )  
      |                   ~  
   67 |                 / INT32_C(301)  
      |                 ~~~~~~~~~~~~~~  
   68 |             );  
      |             ~  
In file included from ../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/riemann_zeta.hpp:10,  
                 from ../v5.2.0/decimal-5.2.0/include/boost/decimal/cmath.hpp:50:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/riemann_zeta_impl.hpp: In function ‘constexpr int boost::decimal::detail::riemann_zeta_decimal_order(T) requires  is_decimal_floating_point_v<T>’:  
../v5.2.0/decimal-5.2.0/include/boost/decimal/detail/cmath/impl/riemann_zeta_impl.hpp:357:12: warning: useless cast to type ‘int’ [-Wuseless-cast]  
  357 |     return static_cast<int>(n + order_bias);  
```  
  
Would be nice if these warnings were fixed, I used the release `v5.2.0`. I also tend to use `-Wpadded` and got more warnings from those, I wonder if those could also be fixed.

---

## Comment 1

> Username: mborland  
> Created at: 2026-01-09 15:29:11 UTC  
> Url: https://github.com/boostorg/decimal/issues/1291#issuecomment-3729393544  

Is your application actually sensitive to the size of the padding used? If so can I ask what it is? There's no real fixes, I would just pragma ignore all of them.

---

## Comment 2

> Username: edubart  
> Created at: 2026-01-09 15:33:25 UTC  
> Url: https://github.com/boostorg/decimal/issues/1291#issuecomment-3729412710  

> Is your application actually sensitive to the size of the padding used? If so can I ask what it is? There's no real fixes, I would just pragma ignore all of them.  
  
My application requires padding to always be zero initialized, so data structures are reproducible across different platforms/compilers. Otherwise its data structures (which I am attempting to use Boost.Decimal) may contain garbage uninitialized data in the paddings. The policy I am using to avoid garbage uninitialized padding is enforcing them to be explicit through `-Wpadded`.
