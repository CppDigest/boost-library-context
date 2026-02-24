# #9 - Leaf should document whether specializing `leaf::match` is allowed [Closed]

> Username: steveire  
> Created at: 2020-06-22 23:32:26 UTC  
> Updated at: 2020-06-30 06:36:48 UTC  
> Closed at: 2020-06-30 06:36:47 UTC  
> Url: https://github.com/boostorg/leaf/issues/9  

It is possible to specialize `match` in order to match on properties of error types other than `.value`.   
  
Consider that this makes it possible to match on the category of a `std::error_code`, but this should be possible for any user-defined error type:   
  
 https://godbolt.org/z/YRXTtZ  
  
It should be documented whether this kind of specialization of matching is allowed.

---

## Comment 1

> Username: zajo  
> Created at: 2020-06-30 06:36:47 UTC  
> Url: https://github.com/boostorg/leaf/issues/9#issuecomment-651576332  

The match<> api is currently overloaded but I have already changed that in my working branch. Now there will be several separate predicates: `match<>`, `match_value<>` (which matches on `.value`), `match_member<>` (which can match any data member, but requires C++17).
