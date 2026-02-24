# #43 - BOOST_DESCRIBE_STRUCT causing warning -Wgnu-zero-variadic-macro-arguments [Open]

> Username: infn-ke  
> Created at: 2023-10-20 11:48:02 UTC  
> Updated at: 2023-10-20 11:51:17 UTC  
> Url: https://github.com/boostorg/describe/issues/43  

`BOOST_DESCRIBE_STRUCT` causes clang warning `-Wgnu-zero-variadic-macro-arguments`. How to best deal with this in absence of c++20? Set `-Wno-gnu-zero-variadic-macro-arguments`?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-20 11:51:17 UTC  
> Url: https://github.com/boostorg/describe/issues/43#issuecomment-1772593841  

I don't know of a better way.
