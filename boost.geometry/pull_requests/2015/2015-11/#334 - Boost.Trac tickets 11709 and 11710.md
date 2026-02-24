# #334 Boost.Trac tickets 11709 and 11710 [Merged]

> Username: mkaravel  
> Created at: 2015-11-05 13:13:13 UTC  
> Updated at: 2015-11-10 06:27:43 UTC  
> Merged at: 2015-11-10 06:06:17 UTC  
> Closed at: 2015-11-10 06:06:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/334  

This PR addresses two Boost.Trac tickets: [11709](https://svn.boost.org/trac/boost/ticket/11709) and [11710](https://svn.boost.org/trac/boost/ticket/11710).  
###### Ticket [11709](https://svn.boost.org/trac/boost/ticket/11709)  
  
`bg::is_simple()` segfaults if given an empty linestring. In this PR the linestring is first checked for emptiness (before any further computations are performed) and `false` is returned is the linestring is found to be empty. Notice that the actual return value does not really matter, as empty linestrings are considered invalid, which means that the behavior of `bg::is_simple()` is undefined for them (the same holds actually for any BG algorithm operating on invalid input).  
###### Ticket [11710](https://svn.boost.org/trac/boost/ticket/11710)  
  
`bg::is_simple()` used to return `false` for empty multi-geometries. This was inconsistent with the behavior of `bg::is_valid()` which accepts empty multi-geometries as valid. In this PR we change this behavior and `bg::is_simple()` returns `true` for empty multi-geometries.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-11-05 13:48:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/334#issuecomment-154062524  

I'm of course ok with merging. But I'm wondering, maybe we should return `true` for all empty "Range-based" geometries so also Linestrings, Rings and Polygons in case if we decided at some point to consider them as valid?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-11-05 14:37:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/334#issuecomment-154078029  

I deliberated (before your comment) about that and decided that `false` is a bit safer: if `bg::is_simple()` were to return `true` you would be more inclined to believe/expect that they are also valid. This way would entirely miss that `bg::is_valid()` returns `false` for these three geometries and this could create a confusion.  
  
I agree that if we switch to accept the empty linestring, ring and polygon as valid geometries, we would also have to make sure that `bg::is_simple()` is changed accordingly (so more work at that point). I would prefer to postpone this change for that moment rather that do it now for the reason I explain above.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-11-05 19:48:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/334#discussion_r44059854  

I agree with this, an empty multi-geometry should be considered as simple.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-11-05 19:50:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/334#issuecomment-154171147  

I agree with your reasoning and the commit. OK with merging. Thanks.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-11-06 11:20:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/334#issuecomment-154384590  

@awulkiew @barendgehrels I will wait a bit more in case there are other comments and will merge into develop this weekend.

---
