# #329 - Completion token to include diagnostics in exceptions [Closed]

> Username: anarthal  
> Created at: 2024-08-08 11:47:28 UTC  
> Updated at: 2024-08-13 08:48:50 UTC  
> Closed at: 2024-08-13 08:48:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/329  

The current use of `throw_on_error` is verbose and error-prone. With careful design, we can craft a `with_diagnostics` completion token that grabs the `diagnostics*` argument from the initiation args and makes the function throw the adequate exception type.
