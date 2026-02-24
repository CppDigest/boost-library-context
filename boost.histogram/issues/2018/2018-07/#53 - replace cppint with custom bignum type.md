# #53 - replace cppint with custom bignum type [Closed]

> Username: HDembinski  
> Created at: 2018-07-04 23:21:40 UTC  
> Updated at: 2018-07-25 09:06:10 UTC  
> Closed at: 2018-07-25 09:06:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/53  

cppint is producing a lot of warnings and it is a big dependency. we don't need a full bignum type, only a few operations are used. we could roll a custom type here.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-25 09:06:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/53#issuecomment-407686795  

This is probably a lot of work with little gain. It seems like cpp_int is already mostly optimized for our purpose.
