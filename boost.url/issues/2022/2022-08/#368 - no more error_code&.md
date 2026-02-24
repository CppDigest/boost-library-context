# #368 - no more error_code& [Closed]

> Username: vinniefalco  
> Created at: 2022-08-08 00:54:50 UTC  
> Updated at: 2022-08-18 16:05:53 UTC  
> Closed at: 2022-08-18 16:05:53 UTC  
> Url: https://github.com/boostorg/url/issues/368  

Some function signatures still use `error_code&`, these should be changed to `result`. And we should remove `BOOST_URL_ERR` and replace it with `BOOST_URL_RETURN_EC`
