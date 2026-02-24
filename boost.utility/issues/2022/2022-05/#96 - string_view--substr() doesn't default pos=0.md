# #96 - string_view::substr() doesn't default pos=0 [Closed]

> Username: sehe  
> Created at: 2022-05-02 05:47:48 UTC  
> Updated at: 2022-05-02 21:49:19 UTC  
> Closed at: 2022-05-02 21:49:19 UTC  
> Url: https://github.com/boostorg/utility/issues/96  

The standard interface allows `sv.substr()` without arguments, defaulting `pos = 0`.  
  
The Boos Utility implementation lacks the default, so doesn't compile such a call.
