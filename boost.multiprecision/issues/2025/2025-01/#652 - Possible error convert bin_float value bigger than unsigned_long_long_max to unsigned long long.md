# #652 - Possible error convert bin_float value bigger than unsigned_long_long_max to unsigned long long [Closed]

> Username: ckormanyos  
> Created at: 2025-01-20 19:13:31 UTC  
> Updated at: 2025-04-29 15:31:23 UTC  
> Closed at: 2025-04-29 15:31:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/652  

OK so,  
  
- You take a number larger than `::std::numeric_limits<unsigned long long>::max()` and store this in `cpp_bin_float_50`.  
- Then you convert that `cpp_bin_float_50` value to `unsigned long long`  
- I find the wrong answers, or what seem to be wrong. I expect to get the `max()`- value of the integral type.  
- `cpp_dec_float_50` gets it right, if that is what right means.  
- Both backends behave properly for `signed long long`, the signed version of the intger.  
  
```cpp  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <ctime>  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <random>  
  
template<typename FloatType, typename LongIshType>  
auto test() -> void  
{  
  std::mt19937_64 gen { static_cast<typename std::mt19937_64::result_type>(std::clock()) };  
  
  auto dis =  
    std::uniform_real_distribution<float>  
    {  
      static_cast<float>(1.01F),  
      static_cast<float>(1.04F)  
    };  
  
  for(auto i = static_cast<unsigned>(UINT8_C(0)); i < static_cast<unsigned>(UINT8_C(8)); ++i)  
  {  
    static_cast<void>(i);  
  
    using float_type = FloatType;  
  
    using long_type = LongIshType;  
  
    float_type  
      flt_max_more  
      {  
          float_type { (std::numeric_limits<long_type>::max)() }  
        * static_cast<float>((static_cast<int>(i) + 1) * 2) * dis(gen)  
      };  
  
    const long_type conversion_result_max_more { static_cast<long_type>(flt_max_more) };  
  
    std::stringstream strm { };  
  
    strm << "0x" << std::hex << std::uppercase << conversion_result_max_more;  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  std::cout << std::endl;  
}  
  
auto main() -> int  
{  
  test<::boost::multiprecision::cpp_dec_float_50, signed long long>();  
  test<::boost::multiprecision::cpp_bin_float_50, signed long long>();  
  
  test<::boost::multiprecision::cpp_dec_float_50, unsigned long long>();  
  test<::boost::multiprecision::cpp_bin_float_50, unsigned long long>();  
}  
```  
  
I get (the last group is seemingly wrong):  
  
```sh  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
0x7FFFFFFFFFFFFFFF  
  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
0xFFFFFFFFFFFFFFFF  
  
0x72D23FFFFFFFFFD  
0xE6E2FFFFFFFFFFB  
0x2426E7FFFFFFFFF9  
0x15C58FFFFFFFFFF7  
0x348C7FFFFFFFFFF5  
0x72B5F7FFFFFFFFF3  
0x567493FFFFFFFFF1  
0x321AFFFFFFFFFFEF  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-01-20 19:18:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/652#issuecomment-2603106093  

If this is an error, I'm happy to fix it any time soon, since it is actually breaking one of my stress trests for new work right now.  
  
Cc: @jzmaddock

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-29 08:19:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/652#issuecomment-2837903941  

The new test code is simplified:  
  
```cpp  
// Copyright 2025 Christopher Kormanyos  
// Distributed under the Boost Software License, Version 1.0.  
// https://www.boost.org/LICENSE_1_0.txt  
  
#include <boost/core/lightweight_test.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <array>  
#include <cstddef>  
#include <cstdint>  
#include <limits>  
  
namespace local {  
  
template<typename FloatType, typename LongIshType>  
auto test_max_convert() -> void  
{  
  using float_type = FloatType;  
  
  using float_array_type = std::array<float_type, std::size_t { UINT8_C(6) }>;  
  
  const float_array_type factors =  
  {  
    float_type { "0.999" },  
    float_type { "1.011" },  
    float_type { "2.022" },  
    float_type { "3.033" },  
    float_type { "4.044" },  
    float_type { "5.055" }  
  };  
  
  for(std::size_t i = std::size_t { UINT8_C(0) }; i < std::tuple_size<float_array_type>::value; ++i)  
  {  
    static_cast<void>(i);  
  
    using long_type = LongIshType;  
  
    const float_type  
      flt_more_than_max_of_built_in_integral  
      {  
        float_type { (std::numeric_limits<long_type>::max)() } * factors[i]  
      };  
  
    const long_type conversion_result { static_cast<long_type>(flt_more_than_max_of_built_in_integral) };  
  
    if(i == std::size_t { UINT8_C(0) })  
    {  
      BOOST_TEST(conversion_result < (std::numeric_limits<long_type>::max)());  
    }  
    else  
    {  
      BOOST_TEST(conversion_result == (std::numeric_limits<long_type>::max)());  
    }  
  }  
}  
  
} // namespace local  
  
auto main() -> int  
{  
  local::test_max_convert<::boost::multiprecision::cpp_dec_float_50, unsigned long long>();  
  
  local::test_max_convert<::boost::multiprecision::cpp_bin_float_50, unsigned long long>();  
  
  return boost::report_errors();  
}  
```
