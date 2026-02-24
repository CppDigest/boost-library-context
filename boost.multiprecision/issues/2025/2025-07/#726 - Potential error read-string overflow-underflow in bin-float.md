# #726 - Potential error read-string overflow/underflow in bin-float? [Open]

> Username: ckormanyos  
> Created at: 2025-07-31 09:35:26 UTC  
> Updated at: 2025-07-31 09:36:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/726  

Hi folks,  
  
Here is an interesting case that I found while doing extreme amounts of enhanced edge-case testing in Multiprecision. There is a reduced (albeit still rather long) test in the code sample below.  
  
The test is intended to read the string input and check the expected non-normal-ness for overflow and underflow. When you read the string that overflows, you sould get `fpclassify(flt_ovf) == FP_INFINITE` and when you read the string that underflows, you should get `fpclassify(flt_und) == FP_ZERO`.  
  
And it works for `cpp_dec_float` and for the newly-created `cpp_double_double`. But these tests fail for `cpp_bin_float`.  
  
I believe the failure is non-bogus and might represent an actual bug or weakness. And it is within the string read of `cpp_bin_float`.  
  
I don't see a real hurry to look into or (if needed) correct this one, we can do that after August and for the next release cycle.  
  
Cc: @jzmaddock and @mborland  
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/multiprecision/cpp_double_fp.hpp>  
  
#include <boost/core/lightweight_test.hpp>  
  
#include <limits>  
#include <typeinfo>  
  
template<typename FloatType> auto my_generator_overflow() noexcept -> FloatType;  
template<typename FloatType> auto my_generator_underflow() noexcept -> FloatType;  
  
namespace local  
{  
  template<class FloatType>  
  bool test_edges_ovf_und()  
  {  
    using float_type = FloatType;  
  
    const float_type flt_ovf = ::my_generator_overflow<float_type>();  
    const float_type flt_und = ::my_generator_underflow<float_type>();  
  
    const bool result_ovf_is_ok { (fpclassify(flt_ovf) == FP_INFINITE) && isinf(flt_ovf) };  
    const bool result_und_is_ok { (fpclassify(flt_und) == FP_ZERO) };  
  
    BOOST_TEST(result_ovf_is_ok);  
    BOOST_TEST(result_und_is_ok);  
  
    const bool result_is_ok { (result_ovf_is_ok && result_und_is_ok) };  
  
    return result_is_ok;  
  }  
} // namespace local  
  
auto main() -> int  
{  
  {  
    using dec_float_backend_type = boost::multiprecision::cpp_dec_float<50>;  
    using dec_float_type = boost::multiprecision::number<dec_float_backend_type, boost::multiprecision::et_off>;  
  
    std::cout << "Testing type: " << typeid(dec_float_type).name() << std::endl;  
  
    static_cast<void>(local::test_edges_ovf_und<dec_float_type>());  
  }  
  
  #if 1  
  {  
    using bin_float_backend_type = boost::multiprecision::cpp_bin_float<50>;  
    using bin_float_type = boost::multiprecision::number<bin_float_backend_type, boost::multiprecision::et_off>;  
  
    std::cout << "Testing type: " << typeid(bin_float_type).name() << std::endl;  
  
    // TBD: This seemingly trivial test fails for cpp_bin_float.  
    static_cast<void>(local::test_edges_ovf_und<bin_float_type>());  
  }  
  #endif  
  
  {  
    using double_float_type = boost::multiprecision::cpp_double_double;  
  
    std::cout << "Testing type: " << typeid(double_float_type).name() << std::endl;  
  
    static_cast<void>(local::test_edges_ovf_und<double_float_type>());  
  }  
  
  return boost::report_errors();  
}  
  
template<class FloatType>  
static auto  
  overflow_expval_maker  
  {  
    []()  
    {  
      std::stringstream strm { };  
  
      using float_type = FloatType;  
  
      strm << std::numeric_limits<float_type>::max_exponent10 + 4;  
  
      return strm.str();  
    }  
  };  
  
template<class FloatType>  
static auto  
  underflow_expval_maker  
  {  
    []()  
    {  
      std::stringstream strm { };  
  
      using float_type = FloatType;  
  
      strm << std::numeric_limits<float_type>::min_exponent10 - 4;  
  
      return strm.str();  
    }  
  };  
  
template<typename FloatType>  
auto my_generator_overflow() noexcept -> FloatType  
{  
  using float_type = FloatType;  
  
  const float_type flt_ovf("1.23E" + overflow_expval_maker<float_type>());  
  
  const float_type result(flt_ovf);  
  
  return result;  
}  
  
template<typename FloatType>  
auto my_generator_underflow() noexcept -> FloatType  
{  
  using float_type = FloatType;  
  
  float_type flt_und("1.23E" + underflow_expval_maker<float_type>());  
  
  const float_type result(flt_und);  
  
  return result;  
}  
```
