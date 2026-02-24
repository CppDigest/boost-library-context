# #255 - accelerate serialization of storages that use accumulators [Open]

> Username: HDembinski  
> Created at: 2019-11-27 13:51:37 UTC  
> Updated at: 2019-11-27 13:51:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/255  

The storages that use accumulators are currently not accelerated with the array_view, this should be fixed, as it could lead to very slow serialization on some archive backends.
