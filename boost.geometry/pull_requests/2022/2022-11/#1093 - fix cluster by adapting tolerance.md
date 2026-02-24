# #1093 [overlay] fix cluster by adapting tolerance [Merged]

> Username: barendgehrels  
> Created at: 2022-11-16 13:10:26 UTC  
> Updated at: 2022-11-23 13:35:27 UTC  
> Merged at: 2022-11-23 13:20:43 UTC  
> Closed at: 2022-11-23 13:20:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1093  

Fixes #1081

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-11-16 13:11:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1093#pullrequestreview-1182542662  

📁 doc/release_notes.qbk

```diff
  27 | 
  28 | * [@https://github.com/boostorg/geometry/issues/1076 1076] Union: in rare cases it might miss one polygon
  29 |+ * [@https://github.com/boostorg/geometry/issues/1081 1081] Union: due to precision it might miss interior rings
```

> Username: barendgehrels  
> Created_at: 2022-11-16 13:11:51 UTC  
> Updated_at: 2022-11-16 13:11:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1093#discussion_r1023976119  

@vissarion if this will not make it in `1.81` then I will update the PR (later)


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-11-16 14:09:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1093#pullrequestreview-1182654043  

Thanks!   
  
So threshold `1000` was too large and now `3` works for all the old cases plus the issue case. Is it possible to make this threshold adaptive in the future? Does it depend on the input?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-11-16 15:31:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1093#issuecomment-1317205469  

> Thanks!  
>   
> So threshold `1000` was too large and now `3` works for all the old cases plus the issue case. Is it possible to make this threshold adaptive in the future? Does it depend on the input?  
  
That's right.  
  
Yes, it might be possible to make it adaptive. I will study that later. For that I should create a new issue. It is convenient to use the input of the reported #1081 issue.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2022-11-16 15:34:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1093#issuecomment-1317210604  

> > Thanks!  
> > So threshold `1000` was too large and now `3` works for all the old cases plus the issue case. Is it possible to make this threshold adaptive in the future? Does it depend on the input?  
>   
> That's right.  
>   
> Yes, it might be possible to make it adaptive. I will study that later. For that I should create a new issue. It is convenient to use the input of the reported #1081 issue.  
  
Made it right away #1094

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2022-11-23 12:58:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1093#issuecomment-1325017567  

> Thanks!  
>   
> So threshold `1000` was too large and now `3` works for all the old cases plus the issue case. Is it possible to make this threshold adaptive in the future? Does it depend on the input?  
  
It's now `1` because the @Mitsuhiko-Matsukawa reported another case failing with `3`. This causes one regression in Buffer. But it seems to me that `1` makes more sense as a value than `3` so I decided to mark that case as a failure, and go for `1`. The new testcase is added as well.  
  
We should later look at that cluster-issue again, possibly using an adaptive approach.

---
