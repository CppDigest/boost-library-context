# #262 Use just ::value where is possible [Merged]

> Username: Kojoley  
> Created at: 2019-03-19 18:11:55 UTC  
> Updated at: 2019-03-29 20:26:18 UTC  
> Merged at: 2019-03-19 19:34:12 UTC  
> Closed at: 2019-03-19 19:34:12 UTC  
> Url: https://github.com/boostorg/gil/pull/262  

Fixes VS2015 failures.  
  
Usually all boolean (like `is_*`, `or`, `and`) and integral (like `*size*`, `*num*`) metafunctions have `value` member.  
  
### References  
  
* Should help to fix #261

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-03-19 18:27:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/262#pullrequestreview-216343499  

LGTM. Thanks for making the clean up!

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-03-19 19:34:56 UTC  
> Url: https://github.com/boostorg/gil/pull/262#issuecomment-474544729  

@Kojoley Thank you!  
  
@stefanseefeld Should this go to Boost 1.70?

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2019-03-19 19:58:16 UTC  
> Url: https://github.com/boostorg/gil/pull/262#issuecomment-474554244  

Given that this is merely a cosmetic fix, I don't think this qualifies.

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-03-19 20:01:11 UTC  
> Updated_at: 2019-03-19 20:02:24 UTC  
> Url: https://github.com/boostorg/gil/pull/262#issuecomment-474555212  

It fixes compilation for VS2015 users, doesn't it?  
  
I don't use VS2015 myself and I don't care about it, but there are users who do, apparently.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2019-03-19 20:03:31 UTC  
> Url: https://github.com/boostorg/gil/pull/262#issuecomment-474556098  

Oh, sorry, I had missed that. In this case it clearly does ! :-)  
(But rather than cherry-picking I'd like to do another merge, but I'll ask the RM first.)

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-03-19 20:04:27 UTC  
> Url: https://github.com/boostorg/gil/pull/262#issuecomment-474556427  

No problem. Good we've clarified that. Thanks

---
