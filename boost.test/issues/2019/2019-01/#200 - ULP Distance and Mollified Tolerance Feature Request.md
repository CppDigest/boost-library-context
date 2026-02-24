# #200 - ULP Distance and Mollified Tolerance Feature Request [Open]

> Username: NAThompson  
> Created at: 2019-01-25 17:04:09 UTC  
> Updated at: 2019-04-28 20:13:44 UTC  
> Url: https://github.com/boostorg/test/issues/200  

We have a use case for more fine-grained testing of floating point numbers in Boost.Math, and were wondering if there's any interest in adding two new tests to Boost.Test: `CHECK_MOLLIFIED_CLOSE` and `CHECK_ULP_CLOSE`. In addition, we are spitting out metadata on test failures (standard representation, hexadecimal representation, ULP distance, and mollified error) which is not currently printed in Boost.Test, and returning a `bool` on failures that allows more metadata attachment in the testing code.  
  
I have toy implementations of these macros here:  
  
```cpp  
#ifndef BOOST_MATH_TEST_TEST_HPP  
#define BOOST_MATH_TEST_TEST_HPP  
#include <atomic>  
#include <iostream>  
#include <iomanip>  
#include <boost/assert.hpp>  
#include <boost/math/special_functions/next.hpp>  
#include <boost/core/demangle.hpp>  
  
  
namespace boost { namespace math { namespace  test {  
  
namespace detail {  
    static std::atomic<size_t> global_error_count = 0;  
}  
  
template<class Real>  
bool check_mollified_close(Real expected, Real computed, Real tol, std::string const & filename, std::string const & function, int line)  
{  
    BOOST_ASSERT_MSG(tol >= 0, "Tolerance must be non-negative.");  
    using std::max;  
    using std::abs;  
    Real denom = max(abs(expected), Real(1));  
    Real mollified_relative_error = abs(expected - computed)/denom;  
    if (mollified_relative_error > tol)  
    {  
        std::ios_base::fmtflags f( std::cerr.flags() );  
        std::cerr << std::setprecision(3);  
        std::cerr << "\033[0;31mError at " << filename << ":" << function << ":" << line << ":\n"  
                  << " \033[0m Mollified relative error in " << boost::core::demangle(typeid(Real).name())<< " precision is " << mollified_relative_error  
                  << ", which exceeds " << tol << ", error/tol  = " << mollified_relative_error/tol << ".\n"  
                  << std::setprecision(std::numeric_limits<Real>::digits10) << std::showpos  
                  << "  Expected: " << std::defaultfloat << std::fixed << expected << std::hexfloat << " = " << expected << "\n"  
                  << "  Computed: " << std::defaultfloat << std::fixed << computed << std::hexfloat << " = " << computed << "\n"  
                  << std::defaultfloat  
                  << "  ULP distance: " << abs(boost::math::float_distance(expected, computed)) << "\n";  
        std::cerr.flags(f);  
        ++detail::global_error_count;  
        return false;  
    }  
    return true;  
}  
  
template<class Real1, class Real2>  
bool check_ulp_close(Real1 expected1, Real2 computed, size_t ulps, std::string const & filename, std::string const & function, int line)  
{  
    using std::max;  
    using std::abs;  
    BOOST_ASSERT_MSG(sizeof(Real1) >= sizeof(Real2),  
                     "The expected number must be computed in higher (or equal) precision than the number being tested.");  
  
    Real2 expected = Real2(expected1);  
    Real2 dist = abs(boost::math::float_distance(expected, computed));  
    if (dist > ulps)  
    {  
        Real2 denom = max(abs(expected), Real2(1));  
        Real2 mollified_relative_error = abs(expected - computed)/denom;  
        std::ios_base::fmtflags f( std::cerr.flags() );  
        std::cerr << std::setprecision(3);  
        std::cerr << "\033[0;31mError at " << filename << ":" << function << ":" << line << ":\n"  
                  << " \033[0m ULP distance in " << boost::core::demangle(typeid(Real2).name())<< " precision is " << dist  
                  << ", which exceeds " << ulps;  
                  if (ulps > 0)  
                  {  
                      std::cerr << ", error/ulps  = " << dist/static_cast<Real2>(ulps) << ".\n";  
                  }  
                  else  
                  {  
                      std::cerr << ".\n";  
                  }  
        std::cerr << std::setprecision(std::numeric_limits<Real2>::digits10) << std::showpos  
                  << "  Expected: " << std::defaultfloat << std::fixed << expected << std::hexfloat << " = " << expected << "\n"  
                  << "  Computed: " << std::defaultfloat << std::fixed << computed << std::hexfloat << " = " << computed << "\n"  
                  << std::defaultfloat  
                  << "  Mollified relative error: " << mollified_relative_error << "\n";  
        std::cerr.flags(f);  
        ++detail::global_error_count;  
        return false;  
    }  
    return true;  
}  
  
  
int report_errors()  
{  
    if (detail::global_error_count > 0)  
    {  
        std::cerr << "\033[0;31mError count: " << detail::global_error_count << "\n";  
        detail::global_error_count = 0;  
        return 1;  
    }  
    std::cout << "\x1B[32mNo errors detected.\n";  
    return 0;  
}  
  
}}}  
  
#define CHECK_MOLLIFIED_CLOSE(X, Y, Z) boost::math::test::check_mollified_close< typename std::remove_reference<decltype((Y))>::type>((X), (Y), (Z), __FILE__, __func__, __LINE__)  
  
#define CHECK_ULP_CLOSE(X, Y, Z) boost::math::test::check_ulp_close((X), (Y), (Z), __FILE__, __func__, __LINE__)  
  
#endif  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-12 00:38:15 UTC  
> Url: https://github.com/boostorg/test/issues/200#issuecomment-462557200  

Thanks! I guess what is even more important is to provide a proper documentation on what those checks are doing. I could not find anything about "mollified", do you have a pointer on how this works?  
  
I am not sure I will be able to include this for the 1.70 release though. Hope this is ok.  
  
(Side note, the link to boost.test floating point documentation from https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/float_comparison.html is a dead link.)

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-02-12 18:12:03 UTC  
> Url: https://github.com/boostorg/test/issues/200#issuecomment-462871367  

[Here's](https://math.stackexchange.com/a/2486241/24355) a definition of the mollifier. The idea is that as x->0, you should consider absolute error, and when |x|>>1, the relative error is more natural. The mollifier interpolates between these two conditions.

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-04-27 20:02:18 UTC  
> Updated at: 2019-04-28 20:13:44 UTC  
> Url: https://github.com/boostorg/test/issues/200#issuecomment-487315527  

I have pulled some bugs out of the features by doing some [nan checks](https://raw.githubusercontent.com/boostorg/math/b14975fa7f8eb49aec89aa8f51fb1615afe126b5/test/math_unit_test.hpp)  
  
I would happily write the documentation and submit a PR for this, but I'm a bit confused about where this would go. Could you give me a few pointers?
