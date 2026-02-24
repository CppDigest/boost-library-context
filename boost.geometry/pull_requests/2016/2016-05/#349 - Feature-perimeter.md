# #349 Feature/perimeter [Merged]

> Username: vissarion  
> Created at: 2016-05-25 12:01:12 UTC  
> Updated at: 2016-06-21 14:57:53 UTC  
> Merged at: 2016-05-28 12:08:34 UTC  
> Closed at: 2016-05-28 12:08:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/349  

Perimeter tests for cartesian, spherical and geographic coordinate systems.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-05-25 13:17:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#issuecomment-221572748  

Thanks a lot Vissarion for this new PR! It is looking very good. Only a few minor details.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2016-05-25 13:22:26 UTC  
> Updated_at: 2016-05-26 07:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#discussion_r64570737  

Should not start with a capital, and curly brace is normally placed on next line in our conventions.  
  
Maybe POLYGON_CASES_HPP can be preceded by perimeter, for in case there is another file like this (might be probable)  
  
The _arr suffix is not necessary in my opinion, I usually have things like:  
  
static std::string case_recursive_boxes_28[2] =  
{  
    "MULTIPOLYGON(((5 1,5 0,4 0,4 2,5 3,5 1)),((4 2,3 2,3 3,4 3,4 2)))",  
    "MULTIPOLYGON(((2 2,2 3,3 3,4 2,3 1,2 1,2 2)),((3 4,4 3,3 3,3 4)),((4 2,5 2,4 1,4 2)))"  
};  
  
The poly prefix (which I did not use) is actually quite useful.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2016-05-25 13:23:42 UTC  
> Updated_at: 2016-05-26 07:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#discussion_r64570920  

Right, I did not do it like this, but it makes sense.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2016-05-25 13:25:40 UTC  
> Updated_at: 2016-05-26 07:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#discussion_r64571190  

Minor thing, after for we always put a space

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2016-05-25 13:26:35 UTC  
> Updated_at: 2016-05-26 07:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#discussion_r64571433  

Usually indented

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2016-05-25 13:27:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#issuecomment-221575555  

I'm OK with merging

---

## Comment 7

> Username: vissarion  
> Created_at: 2016-05-25 13:41:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#issuecomment-221579383  

Thanks a lot Barend for the comments.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2016-05-25 15:22:38 UTC  
> Updated_at: 2016-05-26 07:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#discussion_r64593600  

Names should not start with capital letters.  
  
Ok so you use vectors to call the cases in a loop (only for Polygons) assuming that the expected results are always the same. So if some different results were expected it should be changed. Then you should either write additional cases by yourself or instead of storing only WKTs, store also expected results. I see the intent but in a long run this approach is either overcomplicated or not good enough depending on how you look at it. Though it could stay as it is for now.

---

## Comment 9

> Username: vissarion  
> Created_at: 2016-05-26 07:43:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/349#issuecomment-221800298  

Thanks Adam for the feedback. I was also skeptical about that. I made it a bit simpler.

---
