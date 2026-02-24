# #354 graph/labeled_graph: added remove_labeled_vertex [Merged]

> Username: DeuceBox  
> Created at: 2023-11-17 17:51:56 UTC  
> Updated at: 2023-11-28 00:51:50 UTC  
> Merged at: 2023-11-28 00:48:46 UTC  
> Closed at: 2023-11-28 00:48:46 UTC  
> Url: https://github.com/boostorg/graph/pull/354  

I took this patch from this ticket:  
https://svn.boost.org/trac/boost/ticket/9493  
which no longer exists.  
  
But I believe the original ticket issue was identical to https://github.com/boostorg/graph/issues/167 which this patch fixes.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-11-18 09:12:22 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1817452734  

I'm looking on my phone so I might be wrong, but it looks like it's running GCC 4.X in the CI, which I thought I removed. Are you up to date on develop?

---

## Comment 2

> Username: DeuceBox  
> Created_at: 2023-11-18 22:41:27 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1817667619  

> I'm looking on my phone so I might be wrong, but it looks like it's running GCC 4.X in the CI, which I thought I removed. Are you up to date on develop?  
  
You're correct that my branch was quite far behind develop. I just rebased on the latest develop, pushed again, and the tests were still failing. It appears that the original patch I preserved from the original svn ticket/tracker only accounted for unique associative containers, and not a random access / vector container that the tests use.   
  
I did my best to update the patch and mimic the tag dispatch approach used by other methods to add support to `remove_labeled_vertex` for random access containers and multiple associative containers as well, and pushed that updated branch now,

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-11-20 02:51:03 UTC  
> Updated_at: 2023-11-20 02:51:35 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1818145954  

Looks good. Could you please modify an existing unit test for labelled graphs to assert that the change is effective?  
  
I.e., change a unit test so that it _fails_ without this change, then put it together with your changes so that it passes.  
  
PS. And if there is no existing unit test for labelled graphs... please write a really simple one.  :)

---

## Comment 4

> Username: DeuceBox  
> Created_at: 2023-11-20 13:44:50 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1819091936  

I've added a test for removing a labeled vertex, based on the original https://github.com/boostorg/graph/issues/167 post's code snippet. I tested that it fails without my patch, and passes with my patch.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-11-21 02:58:48 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1820135989  

> I've added a test for removing a labeled vertex, based on the original #167 post's code snippet. I tested that it fails without my patch, and passes with my patch.  
  
That's great.   
  
It does make me wonder about duplicate labels, though -- what happens? Is it even possible? If duplicate labels are possible in any circumstance, then we need a test to confirm what happens.

---

## Comment 6

> Username: DeuceBox  
> Created_at: 2023-11-27 18:46:41 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1828420288  

Added a test that uses a multi associative container and adds two vertices with the same label, and then removes them one by one, asserting the graph vertex count at each stage.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2023-11-27 23:29:57 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1828800666  

> Added a test that uses a multi associative container and adds two vertices with the same label, and then removes them one by one, asserting the graph vertex count at each stage.  
  
Fantastic. As soon as the CI is green, I'll merge it.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2023-11-28 00:51:48 UTC  
> Url: https://github.com/boostorg/graph/pull/354#issuecomment-1828886706  

Thanks so much for fixing a long-standing bug!

---
