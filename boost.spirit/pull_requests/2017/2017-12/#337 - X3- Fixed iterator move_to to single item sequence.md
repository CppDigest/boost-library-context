# #337 X3: Fixed iterator move_to to single item sequence [Merged]

> Username: Kojoley  
> Created at: 2017-12-19 22:16:33 UTC  
> Updated at: 2017-12-20 22:55:55 UTC  
> Merged at: 2017-12-20 22:55:52 UTC  
> Closed at: 2017-12-20 22:55:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/337  

This affects lazy attribute constructing parsers, which exposes not an actual  
attribute, but a pair of iterators (like `string` or `attr("...")`).

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-19 22:42:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/337#issuecomment-352909102  

Nice. I needed that.

---
