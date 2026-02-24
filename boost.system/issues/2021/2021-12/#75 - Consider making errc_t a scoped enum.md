# #75 - Consider making errc_t a scoped enum [Open]

> Username: pdimov  
> Created at: 2021-12-10 22:55:20 UTC  
> Updated at: 2021-12-10 22:56:27 UTC  
> Url: https://github.com/boostorg/system/issues/75  

As is done on branch https://github.com/boostorg/system/tree/feature/scoped-errc.  
  
Unfortunately, this will probably break tons of existing code that relies on the implicit conversion to int.
