# #341 - Add set-theoretic functions for hana::map [Closed]

> Username: shreyans800755  
> Created at: 2017-04-26 05:47:51 UTC  
> Updated at: 2017-06-24 19:58:57 UTC  
> Closed at: 2017-06-24 19:58:51 UTC  
> Url: https://github.com/boostorg/hana/issues/341  

Add intersection and difference interface for hana::map like operation<map_tag, map_tag>.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-05-02 18:13:10 UTC  
> Url: https://github.com/boostorg/hana/issues/341#issuecomment-298716275  

From Gitter (posting here because it's easier to track discussions, rationales & al):  
  
> For intersection of `hana::map`, if there's a key k in both maps `m1` and `m2` with values respectively `v1` and `v2`. What should the intersection function return? I can see couple of options:  
> 1. Return `hana::map` with pair `{k: v1}`  
> 2. Return `hana::map` with pair `{k: pair{v1, v2}}`  
  
A third option is to return `hana::map` with pair `{k, v2}`. However, I think we should be consistent with `hana::union_` and return `{k: v1}`. What do you think?

---

## Comment 2

> Username: shreyans800755  
> Created at: 2017-05-03 07:40:26 UTC  
> Url: https://github.com/boostorg/hana/issues/341#issuecomment-298842589  

Yeah, I think that's better. 2nd option looks like variation of multimap. By the way, is there any plan for including multimap and multiset in hana ?

---

## Comment 3

> Username: ldionne  
> Created at: 2017-05-17 17:19:33 UTC  
> Url: https://github.com/boostorg/hana/issues/341#issuecomment-302162034  

> By the way, is there any plan for including multimap and multiset in hana?  
  
Sorry for the late reply, I somehow missed that. Right now, there is no plan to have that, but I would welcome a PR that adds them (if they can be reasonably compile-time efficient).

---

## Comment 4

> Username: ldionne  
> Created at: 2017-06-24 19:58:51 UTC  
> Url: https://github.com/boostorg/hana/issues/341#issuecomment-310862404  

Closed by #350.
