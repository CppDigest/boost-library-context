# #445 - boost::urls::error::success has no/wrong message [Closed]

> Username: sehe  
> Created at: 2022-08-22 11:24:15 UTC  
> Updated at: 2022-09-06 22:02:11 UTC  
> Closed at: 2022-09-06 22:02:11 UTC  
> Url: https://github.com/boostorg/url/issues/445  

In the implementation there's a `default:` case label causing missed error-codes to get the first message from the next case label instead:  
  
 (which was "bad alpha" until recently and "illegal_null" in the review candidate):  
  
https://github.com/CPPAlliance/url/blob/master/include/boost/url/impl/error.ipp#L34  
  
IDEA: maybe the `default:` should be omitted, allowing some compilers to emit a diagnostic if enumeration members are missing.
