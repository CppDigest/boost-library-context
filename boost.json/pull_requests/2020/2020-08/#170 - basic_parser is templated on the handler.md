# #170 basic_parser is templated on the handler [Merged]

> Username: vinniefalco  
> Created at: 2020-08-18 20:53:37 UTC  
> Updated at: 2020-08-27 21:55:07 UTC  
> Merged at: 2020-08-18 22:32:36 UTC  
> Closed at: 2020-08-18 22:32:36 UTC  
> Url: https://github.com/boostorg/json/pull/170  

`value_builder` is not in its final public state yet

---

## Review 1 [Commented]

> Username: sdkrystian  
> Created_at: 2020-08-18 22:03:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/170#pullrequestreview-469824706  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 186 |-     ~basic_parser()
 236 |+     Handler const&
 237 |+     handler() const noexcept
```

> Username: sdkrystian  
> Created_at: 2020-08-18 20:56:17 UTC  
> Updated_at: 2020-08-18 22:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/170#discussion_r472488144  

I don't think we should bother with this overload, since a `const basic_parser` is useless.

> Username: vinniefalco  
> Created_at: 2020-08-18 22:15:50 UTC  
> Url: https://github.com/boostorg/json/pull/170#discussion_r472523902  

you don't know that, the parse could be complete and someone wants to collect statistics by calling a const member function through a const object


📁 include/boost/json/basic_parser.hpp

```diff
 157 |+ template<class Handler>
 158 | bool
 186 |- basic_parser::
```

> Username: sdkrystian  
> Created_at: 2020-08-18 21:03:13 UTC  
> Updated_at: 2020-08-18 22:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/170#discussion_r472491573  

We can move these to the detail namespace

> Username: vinniefalco  
> Created_at: 2020-08-18 22:16:11 UTC  
> Url: https://github.com/boostorg/json/pull/170#discussion_r472524012  

Move what?


---
