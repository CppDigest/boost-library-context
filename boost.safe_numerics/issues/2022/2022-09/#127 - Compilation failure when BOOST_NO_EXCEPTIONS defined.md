# #127 - Compilation failure when BOOST_NO_EXCEPTIONS defined [Open]

> Username: jplcz  
> Created at: 2022-09-30 13:59:15 UTC  
> Updated at: 2022-09-30 13:59:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/127  

There should be ``void operator()`` instead of ``trap_exception()``  
  
https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/include/boost/safe_numerics/exception_policies.hpp#L80
