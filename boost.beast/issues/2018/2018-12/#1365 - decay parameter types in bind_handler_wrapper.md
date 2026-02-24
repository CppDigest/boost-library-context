# #1365 - decay parameter types in bind_handler_wrapper [Closed]

> Username: vinniefalco  
> Created at: 2018-12-11 22:35:43 UTC  
> Updated at: 2018-12-31 01:30:46 UTC  
> Closed at: 2018-12-31 01:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1365  

To reduce instantiations, the list of template parameters to the bind handler wrapper should be decayed first. Currently they are decayed only when they reach the tuple.
