# #443 Replace mpl::find_if+is_same with find [Merged]

> Username: Kojoley  
> Created at: 2019-01-23 12:24:45 UTC  
> Updated at: 2019-01-25 18:56:22 UTC  
> Merged at: 2019-01-25 18:56:20 UTC  
> Closed at: 2019-01-25 18:56:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/443  

From `mpl::find` docs:  
> `typedef find<s,t>::type i;`  
> Semantics: Equivalent to `typedef find_if<s, is_same<_,t> >::type i;`

---

## Comment 1

> Username: djowel  
> Created_at: 2019-01-23 13:20:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/443#issuecomment-456798651  

duh! of course it is. good find (pun intentional)! :-)

---
