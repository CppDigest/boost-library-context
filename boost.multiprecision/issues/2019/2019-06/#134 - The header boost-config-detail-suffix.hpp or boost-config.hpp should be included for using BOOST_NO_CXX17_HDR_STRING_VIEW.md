# #134 - The header boost/config/detail/suffix.hpp or boost/config.hpp should be included for using BOOST_NO_CXX17_HDR_STRING_VIEW [Closed]

> Username: BinCaoWR  
> Created at: 2019-06-25 01:57:04 UTC  
> Updated at: 2019-06-29 17:57:15 UTC  
> Closed at: 2019-06-29 17:57:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/134  

The following two files are checking if BOOST_NO_CXX17_HDR_STRING_VIEW is defined. However the header boost/config/detail/suffix.hpp or boost/config.hpp which define BOOST_NO_CXX17_HDR_STRING_VIEW is not included.  
  
multiprecision/include/boost/multiprecision/detail/default_ops.hpp  
multiprecision/include/boost/multiprecision/number.hpp  
  
#ifndef BOOST_NO_CXX17_HDR_STRING_VIEW  
#include <string_view>  
#endif
