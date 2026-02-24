# #343 Update boost::container::basic_string template parameters in path_traits [Closed]

> Username: igaztanaga  
> Created at: 2026-01-24 15:36:08 UTC  
> Updated at: 2026-01-24 20:42:41 UTC  
> Closed at: 2026-01-24 20:42:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/343  

Fixes #342

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2026-01-24 16:07:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/343#pullrequestreview-3701901467  

📁 include/boost/filesystem/detail/path_traits.hpp

```diff
 206 | template< >
 207 |- struct path_source_traits< boost::container::basic_string< char, std::char_traits< char >, void > >
 207 |+ struct path_source_traits< boost::container::basic_string< char, std::char_traits< char >, void, void > >
```

> Username: Lastique  
> Created_at: 2026-01-24 16:07:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/343#discussion_r2724300024  

Do these specializations have to be limited to `void` for options? I.e. is is possible that some set of options may make `path_source_traits` specialization invalid or inapplicable to the `basic_string` specialization?  
  
Similarly below, for `check_convertible` and `dispatch_convertible_impl` overloads.  
  
Basically, the traits and function overloads are intended to be applicable to types that can reasonably be used for constructing and integrating with `filesystem::path`. So the question is if `container::basic_string` options should be limited to just the default `void` for this integration to work.

> Username: igaztanaga  
> Created_at: 2026-01-24 16:23:13 UTC  
> Url: https://github.com/boostorg/filesystem/pull/343#discussion_r2724321347  

I don't have a good answer. Previous specialization had "void" for allocator (default allocator), so I guess "options" can be also considered non-essential.

> Username: Lastique  
> Created_at: 2026-01-24 18:00:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/343#discussion_r2724448238  

Oh, now I see why I hardcoded the allocator type to `void`. Making `check_convertible` a template prevents it from being selected if the argument has some entirely different type that is convertible to one of the string types. Looks like I'll have to hardcode `void` for options as well, unfortunately.


---
