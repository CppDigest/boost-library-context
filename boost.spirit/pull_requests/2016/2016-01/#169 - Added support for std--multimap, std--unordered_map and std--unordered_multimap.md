# #169 Added support for std::multimap, std::unordered_map and std::unordered_multimap. [Merged]

> Username: octopus-prime  
> Created at: 2016-01-25 18:56:38 UTC  
> Updated at: 2016-01-26 20:25:35 UTC  
> Merged at: 2016-01-26 20:25:35 UTC  
> Closed at: 2016-01-26 20:25:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/169  

Added container traits.  
Added test cases.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-01-25 22:24:45 UTC  
> Updated_at: 2016-01-26 19:10:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/169#discussion_r50766422  

There should be a way to generalize this for all containers that support c.insert(x), I think. That way, we don't have to include the files and still allow for other containers.

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2016-01-26 19:05:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/169#issuecomment-175181393  

Good idea. Work is done. Tests are extended.

---

## Comment 3

> Username: djowel  
> Created_at: 2016-01-26 20:25:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/169#issuecomment-175210104  

wonderful.

---
