# #612 - List unpacking [Open]

> Username: Kojoley  
> Created at: 2020-05-29 01:03:25 UTC  
> Updated at: 2021-05-29 17:22:38 UTC  
> Url: https://github.com/boostorg/build/issues/612  

It is quite often happens that you know the size of a list and you would want to give its elements names instead of indexing them. Something like:  
```jam  
local name version = [ MATCH "^(\w+)-(.*)" : $(toolset) ] ;  
```  
However, that syntax is already taken by multiple assignment operation, so in example above both variables will contain the same value. Is it documented? I had no luck finding anything in documentation about it. May be there is list unpacking already too? Or may be we can repurposed the syntax for it?  
I grepped Boost and only found the use in some B2 tests:  
```bash  
>grep --include "[Jj]am[fr]*" --include "*.jam" -PR "^\s*(local\s+)?+(\w+\s+){2,}=" .  
./tools/build/src/build/feature.jam:        local p fullp = $(x[1]) ;  
./tools/build/test/core-language/test.jam:    local v1 v2 = d e f ;  
./tools/build/test/core-language/test.jam:    local v1 v1 = d e f ;  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-06-02 00:47:44 UTC  
> Url: https://github.com/boostorg/build/issues/612#issuecomment-637202463  

It sounds reasonable that we implement a real multi-assign feature as what's already there seems just broken to me. I don't know what the original Perforce Jam behavior was on this. But I don't think it matters. As we abandoned compatibility with that long ago. The semantics I would like to see is to have:  
```  
local first rest = one two three ;  
ECHO >> $(first) ;  
ECHO >> $(rest) ;  
```  
To be:  
```  
>> one  
>> two three  
```

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-06-02 16:03:25 UTC  
> Url: https://github.com/boostorg/build/issues/612#issuecomment-637648404  

> The semantics I would like to see is to have:  
>   
> ```  
> local first rest = one two three ;  
> ECHO >> $(first) ;  
> ECHO >> $(rest) ;  
> ```  
>   
> To be:  
>   
> ```  
> >> one  
> >> two three  
> ```  
  
I do not think it is a good idea, but I indeed would like to have it with a different syntax, something with Python-like `first *rest = one two three ;`

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:07 UTC  
> Url: https://github.com/boostorg/build/issues/612#issuecomment-850868275  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
