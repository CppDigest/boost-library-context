# #1292 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:30:48 UTC  
> Updated at: 2024-08-20 06:15:45 UTC  
> Merged at: 2024-08-20 06:15:45 UTC  
> Closed at: 2024-08-20 06:15:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-07-21 20:45:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1292#pullrequestreview-2190396848  

📁 build.jam

```diff
  29 |+         <library>/boost/multiprecision//boost_multiprecision
  30 |+         <library>/boost/numeric_conversion//boost_numeric_conversion
  31 |+         <library>/boost/polygon//boost_polygon
```

> Username: barendgehrels  
> Created_at: 2024-07-21 20:45:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1685809373  

Is it possible to distinguish between real dependencies (the library uses it)  
and other dependencies?  
Other depencie scan be:  
* optional adaptations  
* dependency for test only  
* dependency for examples only

> Username: barendgehrels  
> Created_at: 2024-07-21 20:49:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1685809902  

Probably not that easy and this is probably generated.  
I'm doubting how we should organize our library such that this is more clear.  
  
For the test, it looks good to me, thanks for your efforts!

> Username: grafikrobot  
> Created_at: 2024-07-24 03:52:10 UTC  
> Updated_at: 2024-07-24 03:52:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1689068645  

We can investigate making tweaks after the initial merge. Optional deps are difficult to deal with. Especially since a goal of these changes is to make it possible for more automatic package manager use. So optional things need a human touch to put in appropriate logic. Maybe add a git issue to investigate later?

> Username: grafikrobot  
> Created_at: 2024-07-24 04:08:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1689080239  

Not sure what you mean by dependencies for test or examples. These are the dependencies as listed here https://pdimov.github.io/boostdep-report/develop/geometry.html .. Which are only for the public library headers.

> Username: barendgehrels  
> Created_at: 2024-07-24 05:30:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1689160220  

All right, it's fine for now, you made already a huge effort in all libraries. Thanks again, great work.  
We will look at it ourselves later.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-07-21 20:47:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1292#pullrequestreview-2190397000  

📁 example/Jamfile

```diff
  25 |- exe 07_b_graph_route_example : 07_b_graph_route_example.cpp ;
  25 |+ # exe 07_a_graph_route_example : 07_a_graph_route_example.cpp : <library>/boost/graph//boost_graph ;
  26 |+ # exe 07_b_graph_route_example : 07_b_graph_route_example.cpp : <library>/boost/graph//boost_graph ;
```

> Username: barendgehrels  
> Created_at: 2024-07-21 20:47:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1685809621  

Indeed, boost graph is only used to show this possible collaboration

> Username: grafikrobot  
> Created_at: 2024-07-24 03:59:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1689072337  

Blarg.. I was supposed to not comment those out though. :-(

> Username: barendgehrels  
> Created_at: 2024-07-24 05:31:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#discussion_r1689160898  

I think it can be kept for now. It's not essential for testing or documentation.  
We (I) should be able to fix it later after this rework has passed.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2024-07-24 05:32:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1292#pullrequestreview-2195679092  

Approved already (for convenience - I realize it's still in draft mode, but the approval will stay)

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2024-07-24 13:19:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1292#pullrequestreview-2196729670  

Thanks, I am also approving this PR.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-07-24 13:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#issuecomment-2247947362  

FYI.. There are more changes coming. Because of general comments from other PRs. So you get to approve again. :-)

---

## Comment 6

> Username: vissarion  
> Created_at: 2024-07-24 13:41:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#issuecomment-2247969748  

> FYI.. There are more changes coming. Because of general comments from other PRs. So you get to approve again. :-)  
  
OK I will review it again when in "ready for review" state. Thanks again.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2024-08-18 16:01:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#issuecomment-2295310770  

Please review and merge this PR at your earliest convenience.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-08-20 06:15:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1292#issuecomment-2298049168  

Still looks great to me, I'll merge.  
Thanks again.

---
