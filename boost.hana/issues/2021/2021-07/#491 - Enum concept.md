# #491 - Enum concept [Open]

> Username: vinipsmaker  
> Created at: 2021-07-29 00:31:16 UTC  
> Updated at: 2021-07-29 00:31:16 UTC  
> Url: https://github.com/boostorg/hana/issues/491  

Boost.Hana currently has `Struct`. I have some code that performs automatic serialization for `Struct` types. However an `Enum` is missing in Boost.Hana to help here further.  
  
> The `Struct` concept [...] it allows accessing the names of the members of a user-defined type, and also the value of those members  
  
For the `Enum` concept, I'd expect a similar mapping, however values should be `hana::int_c<something>` (non-mutable).
