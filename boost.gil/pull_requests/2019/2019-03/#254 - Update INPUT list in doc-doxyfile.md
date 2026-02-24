# #254 Update INPUT list in doc/doxyfile [Merged]

> Username: mloskot  
> Created at: 2019-03-07 08:30:54 UTC  
> Updated at: 2019-03-29 20:27:07 UTC  
> Merged at: 2019-03-07 13:40:28 UTC  
> Closed at: 2019-03-07 13:40:28 UTC  
> Url: https://github.com/boostorg/gil/pull/254  

Make peace with @vinniefalco & @pdimov, not war!  
  
-----  
  
/cc @stefanseefeld Please, have a look at the `INPUT` list, is this correct?  
- I don't know if it is Okey to remove `extension/io`  
- What about `concepts/detail`, do we need it?  
  
We need to fix it.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-03-07 12:38:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/254#pullrequestreview-211758062  

I lack context. Adding 'boost/gil/io' certainly looks right, but removing 'boost/gil/extensiin/io' ? Why ?

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-03-07 12:52:56 UTC  
> Url: https://github.com/boostorg/gil/pull/254#issuecomment-470513974  

The context https://cpplang.slack.com/archives/C27KZLB0X/p1551922965896500  
  
-----  
  
Long time ago, all IO lived in in `boost/gil/extensioin/io`.  
  
Then, we split that into common core IO in `boost/gil/io` and from format-specific IO in `boost/gil/extensioin/io`.  
  
Question is, do we expect to display anything from format-specific IO in `boost/gil/extensioin/io` in the docs? If we do, then `doc/doxyfile` should list `boost/gil/extensioin/io`.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2019-03-07 13:03:37 UTC  
> Url: https://github.com/boostorg/gil/pull/254#issuecomment-470517033  

I see no harm in documenting extensions wirh doxygen, in particular if we talk about them in the other docs.

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-03-07 13:40:02 UTC  
> Url: https://github.com/boostorg/gil/pull/254#issuecomment-470528655  

I did not aim to exclude the extensions. It's just that I was unclear about intended input after recent changes. Restored.

---
