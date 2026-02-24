# #151 - Unit test failed when compiled with Clang and ICC 32 bit [Closed]

> Username: phoebewang  
> Created at: 2019-08-09 13:33:15 UTC  
> Updated at: 2019-08-11 08:14:54 UTC  
> Closed at: 2019-08-11 07:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/151  

Unit test 'test_sf_import_c99.cpp' run fail when built with Clang and ICC 32 bit. There's a small reproducer for this issue:  
  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using namespace boost::math::detail;  
using namespace boost::math::policies;  
using namespace boost::multiprecision;  
  
int main()  
{  
  typedef number<cpp_dec_float<61, long long> > float_61;  
  std::size_t overflow_limit = b2n_overflow_limit<float_61, policy<> >();  
  std::cout << overflow_limit << std::endl;  
  return 0;  
}  
  
GCC gives a value 1722805574 but Clang and ICC gives 0. However, none of them is an expected value.  
The root cause was located at "boost_1_70_0/boost/math/special_functions/detail/bernoulli_details.hpp" line 100:  
return static_cast<std::size_t>(boost::math::tools::toms748_solve(fun, sqrt(double(t)), double(t), tol, max_iter).first) / 2;  
  
The source value is 19151855323961996 beyond the destination std::size_t in 32bit.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-08-11 08:14:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/151#issuecomment-520209406  

Thanks for tracking that down - should now be fixed in the commit referenced above.
