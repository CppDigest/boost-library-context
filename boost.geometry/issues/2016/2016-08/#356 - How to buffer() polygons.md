# #356 - How to buffer() polygons [Closed]

> Username: daviddoria  
> Created at: 2016-08-03 15:26:10 UTC  
> Updated at: 2016-12-27 15:40:59 UTC  
> Closed at: 2016-08-03 20:13:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/356  

In Boost 1.61 it seems as though buffer() does not support polygons. I read that there was an experimental implementation of this (the thread was from 2013) - have I come to the right place to try out this implementation? I looked in the 'extensions' directory here but didn't see anything like that.

---

## Comment 1

> Username: awulkiew  
> Created at: 2016-08-03 19:16:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/356#issuecomment-237341835  

It does but you have to call the buffer() taking strategies. See: http://www.boost.org/doc/libs/1_61_0/libs/geometry/doc/html/geometry/reference/algorithms/buffer/buffer_7_with_strategies.html

---

## Comment 2

> Username: daviddoria  
> Created at: 2016-08-03 19:35:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/356#issuecomment-237346922  

Ah, great news! However, I tried it, and the resulting multi_polygon seems to contain 0 polygons?  
  
Here is the code (creates a unit square and tries to expand it by 1 unit) along with the output:  
http://melpon.org/wandbox/permlink/XJx3DppgYVXTN0Mv  
  
Any thoughts?

---

## Comment 3

> Username: daviddoria  
> Created at: 2016-08-03 20:13:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/356#issuecomment-237357708  

Ah, it seems the points must be specified in clockwise order (there was no error when that was not true, just empty output. It seems to work as expected now, thanks!

---

## Comment 4

> Username: awulkiew  
> Created at: 2016-08-03 22:42:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/356#issuecomment-237397798  

The order of points and closing point must correspond to the `bg::model::polygon<>` template parameters. By default polygons are clockwise and closed. See: http://www.boost.org/doc/libs/1_61_0/libs/geometry/doc/html/geometry/reference/models/model_polygon.html
