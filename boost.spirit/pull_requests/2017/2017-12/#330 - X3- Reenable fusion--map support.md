# #330 X3: Reenable fusion::map support [Merged]

> Username: Kojoley  
> Created at: 2017-12-17 00:05:59 UTC  
> Updated at: 2017-12-18 15:51:51 UTC  
> Merged at: 2017-12-18 15:51:48 UTC  
> Closed at: 2017-12-18 15:51:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/330  

`build_container<char>` specialization was added in 84c0c075 (#5)

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-17 01:06:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/330#issuecomment-352223783  

Oh cool! Hmmm "re-enable"? You mean it was disabled at one point? Perhaps unintentionally?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-17 01:23:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/330#issuecomment-352224468  

Compilation error because of missing include (but that was fixable at user side) and commented out `save_to_assoc_attr<variant<T...> >` specialization (turned off support for sequences where key is a variant).

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-17 01:36:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/330#issuecomment-352224952  

Right. I guess that happened with the incremental port from Qi. Thanks!

---
