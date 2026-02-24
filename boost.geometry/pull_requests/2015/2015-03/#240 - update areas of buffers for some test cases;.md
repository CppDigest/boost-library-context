# #240 [test][algorithms][buffer] update areas of buffers for some test cases; [Closed]

> Username: mkaravel  
> Created at: 2015-03-04 07:35:23 UTC  
> Updated at: 2015-04-28 13:49:32 UTC  
> Closed at: 2015-03-04 10:13:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/240  

these changes are due to the recent changes in the join_round strategy;

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-04 08:23:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/240#issuecomment-77115144  

OK - It looks good. If the area is slightly larger, it is usally a bit better (perfectly round has larger area). There are some cases where it is a bit smaller, or the difference is a bit larger. Did you check it visually too?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-04 08:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/240#issuecomment-77117109  

No I haven't.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-03-04 08:43:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/240#issuecomment-77117297  

I now did for some, it might cause some small bubbles (example in your mailbox)

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-04 10:12:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/240#issuecomment-77129170  

Closing this PR and leaving tests as they were.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-04 10:13:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/240#issuecomment-77129288  

See PR #241 reverting the change in the `join_round` strategy.

---
