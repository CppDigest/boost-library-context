# #264 Fix: remove deprecated include paths [Merged]

> Username: mkaravel  
> Created at: 2015-03-12 00:36:43 UTC  
> Updated at: 2015-04-28 13:45:30 UTC  
> Merged at: 2015-04-05 23:32:39 UTC  
> Closed at: 2015-04-05 23:32:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/264  

This PR is about removing and/or fixing include paths that refer to the deprecated `include/boost/geometry/multi/` directory.  
In some cases the includes are not fixed blindly, but also polished a bit.  
  
The removal is the deprecated paths is not finished with this PR. There is more to be done, but I thought that splitting it into smaller chunks will cause fewer problems to other developers.  
  
The is also a practical issue: to avoid potential merge conflicts with PR #256, I would like to have PR #256 merged also. In that PR I indicated that it can wait for boost 1.59, but it can also be merged (there are no new features/new functionality in there, just re-arranging and polishing code).

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-12 02:16:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#issuecomment-78412105  

I propose to postpone it to the time when everybody has more free time and there is no pressure (after the release).

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-12 08:58:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#issuecomment-78443543  

This PR is ready for merging. See also PR #265 and the PR's description in there.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-04 23:38:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27773168  

Should this include be removed?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-04-04 23:38:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27773171  

Should this include be removed?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-04-04 23:40:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27773183  

And here.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-04-05 09:56:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27776116  

Good point, don't think so

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-04-05 10:36:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27776273  

It is included by `test_simplify.hpp` which is why I removed it.  
Typically when we have a supporting `.hpp` file for a unit test, we do not the includes twice.  
At least this is my understanding and experience with the unit tests I have seen so far.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-04-05 10:41:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27776298  

`io/wkt/read.hpp` was included in `test_length.hpp`.  
So what I did was to replace `io/wkt/read.hpp` by `io/wkt/wkt.hpp` in test_length.hpp`and removed it from this file.  
See also my other comment on the`multi_simplify.cpp` unit test.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-04-05 10:42:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27776302  

`io/wkt/wkt.hpp` is included in `test_unique.hpp`.  
In that respect it is okay to remove from here.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-04-05 11:00:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#discussion_r27776374  

OK for me. It was probably originally included as ".../multi/io/wkt" which is why it was still there. Having it in test_length (etc) is OK

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-04-05 11:00:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/264#issuecomment-89752825  

I'm OK with merging

---
