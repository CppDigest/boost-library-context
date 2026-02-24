# #362 Use the /python//numpy target instead of [ numpy.include ] (fixes #361) [Merged]

> Username: pdimov  
> Created at: 2021-05-24 19:16:59 UTC  
> Updated at: 2021-07-10 09:29:42 UTC  
> Merged at: 2021-05-24 22:54:56 UTC  
> Closed at: 2021-05-24 22:54:56 UTC  
> Url: https://github.com/boostorg/python/pull/362  

This fixes issue #361 by using the `/python//numpy` target. It however requires the corresponding Boost.Build PR https://github.com/boostorg/build/pull/726 to be merged first.

---

## Comment 1

> Username: SoapGentoo  
> Created_at: 2021-05-24 20:59:03 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-847329975  

I've verified that this patch works with multiple python implementations, please merge it.

---

## Comment 2

> Username: thesamesam  
> Created_at: 2021-05-24 21:44:02 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-847355776  

As per my [comment](https://github.com/boostorg/build/pull/726#issuecomment-847355490) on the linked PR for the other side in Build, this resolves my issue.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2021-07-04 19:13:02 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-873646024  

@pdimov , can you please confirm that it's now safe to (re-)merge this PR to devel (so I can merge everything to master before the upcoming deadline) ?

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-07-04 19:25:44 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-873647692  

I can see the change on b2's master branch (https://github.com/boostorg/build/blob/d52f1993d906aa3fd3649a8dd5134402794bf86e/src/tools/python.jam#L1055-L1057), so this should now work. (cc @grafikrobot)

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2021-07-04 19:32:19 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-873648535  

Thanks, I have re-added your commit, and will merge to master before Wednesday.

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-07-07 10:21:12 UTC  
> Url: https://github.com/boostorg/python/pull/362#issuecomment-875483959  

Reminder that it's Wednesday. :-)

---
