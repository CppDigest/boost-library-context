# #119 - [Associative] Set theoretic operations missing from the interface [Closed]

> Username: ldionne  
> Created at: 2015-06-15 18:50:09 UTC  
> Updated at: 2015-08-23 14:40:32 UTC  
> Closed at: 2015-08-23 14:40:32 UTC  
> Url: https://github.com/boostorg/hana/issues/119  

At least the following functions are missing from the interface of associative sequences:  
- [x] set intersection  
- [x] set union  
- [x] set difference  
- [x] symmetric set difference  
- [x] are two sets disjoint?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-19 09:21:49 UTC  
> Url: https://github.com/boostorg/hana/issues/119#issuecomment-132505604  

@rbock IIRC, you were the one to originally request these features on the Boost.Devel mailing list. Right now, I'm struggling to define what should the intersection/union/difference of two maps be. For sets, it's easy because it's just a bunch of keys. But for maps, should the values from the first map or the second map be retained in the resulting container? Should the intersection only contain key/value pairs where both the key **and** the value are equal in both maps? If you have any opinion regarding how this should be treated, please let me know.

---

## Comment 2

> Username: rbock  
> Created at: 2015-08-20 19:10:30 UTC  
> Url: https://github.com/boostorg/hana/issues/119#issuecomment-133125942  

Personally, I would not offer intersection/union/difference for maps.  
  
Maybe if the values themselves were sets. Then the values of the resulting map could be the intersection etc, but that seems like a very special case (and I have no idea if anyone would ever need something like that).

---

## Comment 3

> Username: ldionne  
> Created at: 2015-08-23 12:31:57 UTC  
> Url: https://github.com/boostorg/hana/issues/119#issuecomment-133826425  

> Personally, I would not offer intersection/union/difference for maps.  
  
Makes sense as a first step.
