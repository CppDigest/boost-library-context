# #1191 Modernize noexcept specifications [Merged]

> Username: georgthegreat  
> Created at: 2023-08-28 09:50:26 UTC  
> Updated at: 2023-09-08 10:20:30 UTC  
> Merged at: 2023-09-08 09:59:01 UTC  
> Closed at: 2023-09-08 09:59:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191  

As boost::geometry mandates C++14 since 1.75, I assume this can be merged in C++03-incompatible form.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-08-29 17:10:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1191#pullrequestreview-1600917184  

Looks good to me, indeed C++14 syntax is fine now

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2023-08-29 17:11:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1697839003  

One thing, @vissarion , is it better to split it into two commits?  
One for library code, one for extensions?

---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2023-08-30 09:14:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1191#pullrequestreview-1602267516  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
  49 |-     virtual ~turn_info_exception() throw()
  49 |+     virtual ~turn_info_exception()
  50 |     {}
```

> Username: barendgehrels  
> Created_at: 2023-08-30 09:14:56 UTC  
> Updated_at: 2023-08-30 09:14:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1309957723  

We can better remove these constructors and destructors, in most cases we don't define them.

> Username: georgthegreat  
> Created_at: 2023-08-30 10:20:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1310043340  

Maybe we can merge this as is?  
I will do the cleanup later on.

> Username: barendgehrels  
> Created_at: 2023-08-30 16:05:55 UTC  
> Updated_at: 2023-08-30 16:05:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1310509156  

You probably have to change it anyway, because the commit mixes extensions and normal code...  
But of course, indeed, we could do the cleanup in a separate PR tool.

> Username: georgthegreat  
> Created_at: 2023-08-30 18:20:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1310658531  

I do not understand the problem with the extensions directory.  
  
Could you, please, explain it to me?

> Username: georgthegreat  
> Created_at: 2023-08-30 18:22:33 UTC  
> Updated_at: 2023-08-30 18:22:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1310660433  

Btw, it is unclear if these dtors should be removed or defaulted.  
  
This is more like a codestyle consideration.

> Username: barendgehrels  
> Created_at: 2023-08-31 18:25:51 UTC  
> Updated_at: 2023-08-31 18:25:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#discussion_r1312044375  

Indeed, it is. I think they can all be removed, don’t see what it adds.  
  
About `extensions`: we often merge from develop to master by cherry-picking commits. Master does not contain anything from the `extensions` folder. So then cherry-picking does not work or needs manual editing.


---

## Comment 4

> Username: georgthegreat  
> Created_at: 2023-09-01 15:57:23 UTC  
> Updated_at: 2023-09-01 15:58:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1702971467  

I have split the changes into two commits and removed the dtors as requested

---

## Comment 5

> Username: awulkiew  
> Created_at: 2023-09-02 10:55:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1703799987  

@georgthegreat Since you removed the unneded destructors would you be willing to make some other related improvements as well or would you prefer to merge this as it is?  
  
The improvements I'm thinking about are:  
- the removal of unneeded default constructors or making them  `= default` in case their existance is documentation-related  
- replacement of `virtual` in `what()` declaration with `override`

---

## Comment 6

> Username: georgthegreat  
> Created_at: 2023-09-02 12:01:39 UTC  
> Updated_at: 2023-09-04 09:30:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1703813849  

@awulkiew, I am ok with doing this, but I want this PR merged without this change.  
Other improvements are definitely out of scope of this PR.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2023-09-05 18:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1707106954  

Thanks for splitting it.

---

## Comment 8

> Username: georgthegreat  
> Created_at: 2023-09-08 09:41:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1711381630  

Let's merge it.

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2023-09-08 09:58:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1191#pullrequestreview-1617118815  

Thanks!

---

## Comment 10

> Username: georgthegreat  
> Created_at: 2023-09-08 10:20:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1191#issuecomment-1711433980  

@awulkiew, virtual -> override replacement is here:  
https://github.com/boostorg/geometry/pull/1198/files

---
