# #849 - Compile-time permessage-deflate option [Closed]

> Username: vinniefalco  
> Created at: 2017-10-28 17:41:17 UTC  
> Updated at: 2017-12-31 17:41:09 UTC  
> Closed at: 2017-12-31 17:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/849  

It should be possible to instantiate the websocket stream to make permessage deflate a compile-time option:  
```  
template<class NextLayer, bool enablePermessageDeflate = true>  
class stream;  
```  
  
If `enablePermessageDeflate` is `false` then the extension is never offered or accepted. If it is `true` then the run-time `permesssage_deflate` options are used.  
  
This should allow the resulting executable for websocket programs to be smaller.
