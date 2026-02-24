# #417 - boost::lexical_cast<static_string_n<>> [Closed]

> Username: vinniefalco  
> Created at: 2017-06-03 21:57:53 UTC  
> Updated at: 2017-06-12 04:01:00 UTC  
> Closed at: 2017-06-12 04:01:00 UTC  
> Url: https://github.com/boostorg/beast/issues/417  

This allows conversion for fields without dynamic allocation

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 04:01:00 UTC  
> Url: https://github.com/boostorg/beast/issues/417#issuecomment-307685184  

NACK, `boost::lexical_cast` ain't so great! But we have `string_param` now so this is taken care of.
