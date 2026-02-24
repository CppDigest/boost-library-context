# #374 - default parameter value for throwing functions [Closed]

> Username: vinniefalco  
> Created at: 2022-08-09 01:38:38 UTC  
> Updated at: 2022-08-11 18:06:08 UTC  
> Closed at: 2022-08-11 18:06:08 UTC  
> Url: https://github.com/boostorg/url/issues/374  

Functions like `throw_bad_alloc` can just use a default value of `BOOST_CURRENT_LOCATION` in the function declaration, and this works (tested it).
