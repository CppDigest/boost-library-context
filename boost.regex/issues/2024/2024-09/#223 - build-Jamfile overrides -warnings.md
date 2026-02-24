# #223 - build/Jamfile overrides <warnings> [Closed]

> Username: pdimov  
> Created at: 2024-09-02 08:00:44 UTC  
> Updated at: 2024-09-03 10:31:28 UTC  
> Closed at: 2024-09-03 10:31:28 UTC  
> Url: https://github.com/boostorg/regex/issues/223  

As mentioned in https://github.com/boostorg/regex/issues/219, linking to `/boost/serialization//boost_serialization/<warnings>off` doesn't propagate `<warnings>off` to Regex, which breaks when `warnings-as-errors` is `on`.  
  
That's because build/Jamfile contains  
  
```  
project : requirements  
   # default to all warnings on:  
   <warnings>all  
   : common-requirements <library>$(boost_dependencies)  
   : requirements <library>$(boost_dependencies_private)  
   ;  
```  
  
which overrides the inherited `<warnings>off` with `<warnings>all`.  
  
The comment says "default to all warnings on", but since it's a requirement, it's not a default and is always applied.  
  
It should be put in `default-build` instead.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-02 08:02:27 UTC  
> Updated at: 2024-09-02 08:02:40 UTC  
> Url: https://github.com/boostorg/regex/issues/223#issuecomment-2324074764  

(or removed altogether - `<warnings>all` should be used when testing, not when building.)
