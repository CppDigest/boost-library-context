# #53 - BOOST_PFR_FUNCTIONS_FOR for templated structures [Closed]

> Username: apolukhin  
> Created at: 2020-10-14 15:06:01 UTC  
> Updated at: 2020-12-19 10:57:41 UTC  
> Closed at: 2020-12-19 10:57:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/53  



---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-19 10:57:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/53#issuecomment-748457677  

After some thinking this seems to be a bad idea. Quite a big set of macro is required to provide all the required functionalities: `templated versions` * `heterogeneous versions` * `comparison categories` * ...  
  
`BOOST_PFR_FUNCTIONS_FOR` is meant for usage in most common case. For all the other cases use PFRs comparison functions.
