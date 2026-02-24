# #110 - (Possible) cpp_bin_float_quad to checked_int128_t conversion bug [Closed]

> Username: esakaen  
> Created at: 2019-02-03 18:48:58 UTC  
> Updated at: 2019-03-04 18:37:45 UTC  
> Closed at: 2019-03-04 18:37:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/110  

Whilst the above conversion worked for some test cases, the following error was thrown for the conversion of a `cpp_bin_float_quad` with  a value of `-1.75329768098667154050329834e+26`: "Bitwise operations on negative values results in undefined behavior." It occurred with Visual Studio 2017 as well as with Xcode 10.1 (in both cases using the C++17 flag). Interestingly, it works, i.e., gives the correct result, if the value is converted into an unchecked `int128_t`. Provided that I understood the documentation correctly, an inadmissible conversion should result in a compiler error, which his not the case here.  
  
For convenience, I attached a test program as well as the Visual Studio output and call stack to `is_valid_bitwise_op`  below.  
  
```C++  
#include <iostream>  
#include <exception>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
int main( int argc, char* argv[] )  
{  
  using namespace boost::multiprecision;  
  
  std::cout.precision( 39 );  
  std::cout.setf( std::ios::scientific );  
  
  cpp_bin_float_quad qF( "-175329768098667154050329834" );  
  
  std::cout << qF << std::endl;  
  
  int128_t qI( qF );  
  
  std::cout << qI << std::endl;  
  
  try  
  {  
    checked_int128_t qCI( qF );  
  
    std::cout << qCI << std::endl;  
  }  
  catch ( const std::exception& e )  
  {  
    std::cout << e.what() << std::endl;  
  }  
  
  return 0;  
}  
```  
  
```  
-1.753297680986671540503298340000000000000e+26  
-175329768098667154050329834  
Bitwise operations on negative values results in undefined behavior.  
```  
  
```  
>   LongTypeTest2.exe!boost::multiprecision::backends::is_valid_bitwise_op<128,128,1,void>(const boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void> & result, const boost::mpl::int_<1> & __formal) Line 37 C++  
    LongTypeTest2.exe!boost::multiprecision::backends::eval_left_shift<128,128,1,1,void>(boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void> & result, unsigned __int64 s) Line 435  C++  
    LongTypeTest2.exe!boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void>,0>::operator<<=<int>(int val) Line 667   C++  
    LongTypeTest2.exe!boost::multiprecision::detail::generic_interconvert_float2int<boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void>,boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383> >(boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void> & to, const boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383> & from, const boost::mpl::int_<2> & __formal) Line 492  C++  
    LongTypeTest2.exe!boost::multiprecision::detail::generic_interconvert<boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void>,boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383> >(boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void> & to, const boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383> & from, const boost::mpl::int_<0> & __formal, const boost::mpl::int_<1> & __formal) Line 526  C++  
    LongTypeTest2.exe!boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void>,0>::number<boost::multiprecision::backends::cpp_int_backend<128,128,1,1,void>,0><boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383>,0>(const boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113,2,void,short,-16382,16383>,0> & val, void * __formal) Line 136  C++  
    LongTypeTest2.exe!main(int argc, char * * argv) Line 25 C++  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-03 19:14:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/110#issuecomment-460079373  

That's a weird one.  Smells like a bug, will investigate.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-02-13 19:22:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/110#issuecomment-463331989  

It's a bug, see: https://github.com/boostorg/multiprecision/pull/115

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-03-04 18:37:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/110#issuecomment-469367337  

Fix wll be in the next release.
