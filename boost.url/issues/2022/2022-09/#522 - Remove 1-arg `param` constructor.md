# #522 - Remove 1-arg `param` constructor? [Closed]

> Username: alandefreitas  
> Created at: 2022-09-09 15:57:32 UTC  
> Updated at: 2022-09-11 23:22:01 UTC  
> Closed at: 2022-09-11 23:22:01 UTC  
> Url: https://github.com/boostorg/url/issues/522  

Explore whether `param`s should be implicitly constructible from a single `string_view`.   
  
One problem with that design is that params has the problem of ambiguity because { "key", "value" } can be either a 2-element list or a 1-element param.  
  
If we thing of params as pairs, we could have something like `{"key", boost::none}`, `{"key", nullptr}` or something.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:22:01 UTC  
> Url: https://github.com/boostorg/url/issues/522#issuecomment-1243065540  

Done. it is `urls::no_value` or `nullptr`
