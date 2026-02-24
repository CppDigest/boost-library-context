# #182 - 'argument': conversion from 'ptrdiff_t' to 'int', possible loss of data [Open]

> Username: tmfjr  
> Created at: 2020-11-04 16:52:42 UTC  
> Updated at: 2020-11-04 16:52:42 UTC  
> Url: https://github.com/boostorg/process/issues/182  

boost 1.74.0; Microsoft Visual Studio 2019; C++ v14.27; SDK 10.0.18362.0  
  
#include <boost/process.hpp>  
  
boost\process\pipe.hpp(282,21): warning C4244: 'argument': conversion from 'ptrdiff_t' to 'int', possible loss of data  
  
current work-around in our code:  
  
#pragma warning(push)  
#pragma warning(disable : 4244) // 'argument': conversion from 'ptrdiff_t' to 'int', possible loss of data  
#include <boost/process.hpp>  
#pragma warning(pop)
