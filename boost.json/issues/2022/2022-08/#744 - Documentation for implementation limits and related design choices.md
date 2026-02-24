# #744 - Documentation for implementation limits and related design choices [Open]

> Username: grisumbras  
> Created at: 2022-08-28 04:15:04 UTC  
> Updated at: 2023-06-01 11:00:35 UTC  
> Url: https://github.com/boostorg/json/issues/744  

Documentation would benefit from the  
info on implementation-limits-related design choices.  
  
* Guaranteed roundtrip for `value` -> serialized text -> value (but note that infinity and NaN can be in the original `value` and do not currently serialize properly).  
* JSON inputs where number values cannot be represented losslessly in  
`uint64_t`, `int64_t` and `double`, may render different values when parsed and  
then serialized back, and for extremely big number values can even fail to  
parse (document supported number ranges).  
* Whatever JSON output you can produce with this library, we guarantee it  
can be passed by any common JSON implementation (probably also based on  
`uint64_t` + `int64_t` + `double` implementation) (this hardly can be anything specific, we can only present it as a goal of the library).  
  
Also  
  
* Configuration macros that affect implementation limits  
* Hard-coded/default implementation limits
