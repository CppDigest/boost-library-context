# #109 - Don't call destructors as if they are normal methods (they have side-effects) [Closed]

> Username: HDembinski  
> Created at: 2018-09-26 08:58:13 UTC  
> Updated at: 2018-10-27 17:48:40 UTC  
> Closed at: 2018-10-27 17:48:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/109  

As pointed out by Steven, calling a destructor also calls all destructors of members and bases.  
  
https://wandbox.org/permlink/CtfJSrGZi3jCAk58  
  
No calling of destructors unless you want to happen!
