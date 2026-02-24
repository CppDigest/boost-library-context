# #359 - Client-side SQL: sequence should be an owning type [Closed]

> Username: anarthal  
> Created at: 2024-09-30 17:12:33 UTC  
> Updated at: 2024-10-11 20:17:18 UTC  
> Closed at: 2024-10-11 20:17:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/359  

Following #299, we should make `sequence` owning, because `with_params` is (so we don't get unexpected behavior when using deferred tokens).
