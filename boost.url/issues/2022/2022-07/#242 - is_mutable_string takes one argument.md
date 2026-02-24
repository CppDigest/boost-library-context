# #242 - is_mutable_string takes one argument [Closed]

> Username: vinniefalco  
> Created at: 2022-07-23 22:57:05 UTC  
> Updated at: 2022-08-04 04:20:25 UTC  
> Closed at: 2022-08-04 04:20:25 UTC  
> Url: https://github.com/boostorg/url/issues/242  

`is_mutable_string` should use a private exemplar of _InputIterator_ instead of requiring the type `I`.
