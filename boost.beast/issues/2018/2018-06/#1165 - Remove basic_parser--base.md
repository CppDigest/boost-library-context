# #1165 - Remove basic_parser::base [Open]

> Username: vinniefalco  
> Created at: 2018-06-18 15:44:59 UTC  
> Updated at: 2018-06-18 15:45:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1165  

Call sites can just use `static_cast`, this function doesn't add much and probably won't survive in a TS.
