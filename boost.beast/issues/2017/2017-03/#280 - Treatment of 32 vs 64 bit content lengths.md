# #280 - Treatment of 32 vs 64 bit content lengths [Closed]

> Username: vinniefalco  
> Created at: 2017-03-14 21:44:30 UTC  
> Updated at: 2017-06-13 00:46:14 UTC  
> Closed at: 2017-06-13 00:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/280  

`basic_parser` should represent content lengths as 64 bit unsigned quantities.  
  
However, when reading bodies using the **Reader** concept in 32 bit builds with `message_parser`, the parser should throw `std::length_error` when attempting to read a body that exceeds 2^32 octets.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-13 00:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/280#issuecomment-307974528  

This is done
