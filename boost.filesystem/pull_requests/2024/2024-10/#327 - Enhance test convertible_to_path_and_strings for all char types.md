# #327 Enhance test convertible_to_path_and_strings for all char types [Closed]

> Username: Flamefire  
> Created at: 2024-10-01 08:55:46 UTC  
> Updated at: 2024-10-01 12:28:55 UTC  
> Closed at: 2024-10-01 12:20:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/327  

This extends the test in fbd23ee0e07ff17d486534314f89d1b5fb306824 to include all possible `Source` types accepted by `is_convertible_to_path_source`  
  
See : https://github.com/boostorg/filesystem/blob/fbd23ee0e07ff17d486534314f89d1b5fb306824/include/boost/filesystem/detail/path_traits.hpp#L562-L571

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-10-01 09:45:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/327#issuecomment-2385317726  

This has no effect. The point of the test is to have an ambiguous conversion to a Source type. It doesn't matter which of the candidate types are supported by the original type.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-10-01 11:27:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/327#issuecomment-2385526760  

> This has no effect. The point of the test is to have an ambiguous conversion to a Source type. It doesn't matter which of the candidate types are supported by the original type.  
  
With this enhancement I just want to make sure the ambiguity resolution is not dependent on e.g. the native char type or any of the other possible string-likes.    
The (test) code is very simple and it fits the class name `convertible_to_path_and_strings`, so what does it cost to include it to avoid future regressions?

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-10-01 12:20:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/327#issuecomment-2385631159  

> With this enhancement I just want to make sure the ambiguity resolution is not dependent on e.g. the native char type or any of the other possible string-likes.  
  
`path` will always support constructors/operations taking native strings, so the existing conversion operators are enough to introduce ambiguity. I don't see the point of adding more operators, it doesn't add anything.

---
