# #3 - Add BOOST_LAMBDA2_VERSION, do not #undef macros [Closed]

> Username: pdimov  
> Created at: 2021-03-27 18:08:20 UTC  
> Updated at: 2021-06-27 00:51:34 UTC  
> Closed at: 2021-06-27 00:51:34 UTC  
> Url: https://github.com/boostorg/lambda2/issues/3  

The macros are useful for people who want to define operators the library doesn't provide (yet), and the version macro is useful if a later version of the library adds an operator the user has defined.
