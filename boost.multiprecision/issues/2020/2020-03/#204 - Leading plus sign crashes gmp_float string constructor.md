# #204 - Leading plus sign crashes gmp_float string constructor [Closed]

> Username: ckormanyos  
> Created at: 2020-03-29 12:49:17 UTC  
> Updated at: 2020-03-30 17:47:58 UTC  
> Closed at: 2020-03-30 17:47:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/204  

We often parse strings to construct floating point types in Boost.Multiprecision.  
  
When parsing the string `"+1.23"`, the constructor throws (or crashes) for `gmp_float`. The leading plus sign, however, is tolerated when constructing `cpp_bin_float` and `cpp_dec_float`.  
  
See the code sample below.  
  
For gmp_float, the constructor line goes straightforwardly to `mpf_set_str()`.  
  
Should we catch the leading plus sign and disregard it?  
I do not know if this is a desired change, a breaking change, or not relevant. If we change it, I could handle the change, but preferably for 1.74 after 1.73 since constructor changes can be sensitive.  
  
```  
#include <iostream>  
#include <iomanip>  
  
#define BOOST_MULTIPRECISION_USE_CPP_BIN_FLOAT 1  
#define BOOST_MULTIPRECISION_USE_CPP_DEC_FLOAT 2  
#define BOOST_MULTIPRECISION_USE_GMP_FLOAT     3  
  
#define BOOST_MULTIPRECISION_BIG_FLOAT_TYPE BOOST_MULTIPRECISION_USE_GMP_FLOAT  
  
#if(BOOST_MULTIPRECISION_BIG_FLOAT_TYPE == BOOST_MULTIPRECISION_USE_CPP_BIN_FLOAT)  
#include <boost/multiprecision/cpp_bin_float.hpp>  
using big_float_type = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<72>>;  
#elif(BOOST_MULTIPRECISION_BIG_FLOAT_TYPE == BOOST_MULTIPRECISION_USE_CPP_DEC_FLOAT)  
#include <boost/multiprecision/cpp_dec_float.hpp>  
using big_float_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<72>>;  
#elif(BOOST_MULTIPRECISION_BIG_FLOAT_TYPE == BOOST_MULTIPRECISION_USE_GMP_FLOAT)  
#include <boost/multiprecision/gmp.hpp>  
using big_float_type = boost::multiprecision::number<boost::multiprecision::gmp_float<72>>;  
#else  
#error BOOST_MULTIPRECISION_BIG_FLOAT_TYPE is not defined.  
#endif  
  
int main()  
{  
  // For gmp_float, this line goes straightforwardly to mpf_set_str().  
  const big_float_type bf("+1.23");  
  
  std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10)  
            << bf  
            << std::endl;  
}  
```  
This might be a known issue

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-03-30 17:47:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/204#issuecomment-606145434  

Apparently mpf_set_str doesn't handle a leading "+" character, fixed in develop.
