# #33 - info.hpp: warning C4265 given by Visual Studio [Closed]

> Username: hanspacket  
> Created at: 2020-06-08 17:42:31 UTC  
> Updated at: 2020-12-25 03:50:39 UTC  
> Closed at: 2020-12-25 03:50:39 UTC  
> Url: https://github.com/boostorg/exception/issues/33  

This issue is very similar to https://github.com/boostorg/exception/issues/23: when enabling warning C4265 as error, the following error is triggered in boost/exception/info.hpp:  
  
`info.hpp(157): error C4265: 'boost::exception_detail::error_info_container_impl': class has virtual functions, but destructor is not virtual  
         instances of this class may not be destructed correctly`  
  
The issue can be reproduced using https://github.com/ivsgroup/boost_warnings_minimal_demo, but with a few differences:  
- boost 1.73.0  
- use  `#include <boost/algorithm/hex.hpp>`  
  
I can fix this problem locally by adding `#pragma warning(disable: 4265)` to boost/exception/info.hpp line 25

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-25 00:34:51 UTC  
> Url: https://github.com/boostorg/exception/issues/33#issuecomment-751135669  

I need an exact command line that reproduces the problem. Also, which version of Boost?
