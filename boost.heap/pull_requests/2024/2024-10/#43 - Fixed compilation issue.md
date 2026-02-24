# #43 Fixed compilation issue. [Merged]

> Username: phillip-keldenich  
> Created at: 2024-10-10 19:58:29 UTC  
> Updated at: 2024-10-11 08:23:39 UTC  
> Merged at: 2024-10-11 08:23:38 UTC  
> Closed at: 2024-10-11 08:23:38 UTC  
> Url: https://github.com/boostorg/heap/pull/43  

There was a typo (value_compare() vs. value_comp()), causing compilation errors when value_compare was not default constructible and assertions were enabled.  
  
This fixes my issue #42 .

---
