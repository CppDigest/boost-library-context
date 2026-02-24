# #378 - Compile time check to assert # keywords == arity [Open]

> Username: dyollb  
> Created at: 2022-01-03 14:34:15 UTC  
> Updated at: 2022-01-03 16:18:32 UTC  
> Url: https://github.com/boostorg/python/issues/378  

Experience has shown that C++ signatures may change over time: often arguments are added. Unfortunately, boost python will not complain if developers forget to update the `bp` export code.  
  
`make_function_aux` in [make_function.hpp](https://github.com/boostorg/python/blob/develop/include/boost/python/make_function.hpp) already contains a check that the user did not provide too many keywords.  
It would be useful if we could enable a check to assert that the number of keywords always is exactly equal to the number of arguments.
