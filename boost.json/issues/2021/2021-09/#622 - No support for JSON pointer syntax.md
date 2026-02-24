# #622 - No support for JSON pointer syntax [Closed]

> Username: cmazakas  
> Created at: 2021-09-30 21:22:44 UTC  
> Updated at: 2021-09-30 21:24:01 UTC  
> Closed at: 2021-09-30 21:24:01 UTC  
> Url: https://github.com/boostorg/json/issues/622  

Similar libraries such as nlohmann's or RapidJSON support access to values via the [JSON pointer](https://datatracker.ietf.org/doc/html/rfc6901) syntax.  
  
Boost.JSON should have feature parity in this regard, perhaps introducing `value:at_p(string_view)` member function.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-30 21:24:01 UTC  
> Url: https://github.com/boostorg/json/issues/622#issuecomment-931715344  

Duplicate of https://github.com/boostorg/json/issues/480
