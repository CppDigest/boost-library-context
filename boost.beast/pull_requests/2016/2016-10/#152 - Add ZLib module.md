# #152 Add ZLib module [Closed]

> Username: vinniefalco  
> Created at: 2016-10-24 18:20:06 UTC  
> Updated at: 2017-07-25 17:44:17 UTC  
> Closed at: 2016-11-04 17:34:46 UTC  
> Url: https://github.com/boostorg/beast/pull/152  

The zlib module is a port of ZLib's raw deflate algorithm to header-only C++11.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-10-24 18:30:50 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-255825438  

Reviewers: @JoelKatz @seelabs

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-10-25 16:52:46 UTC  
> Updated_at: 2016-11-04 15:51:38 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-256093599  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/152?src=pr) is 95.95% (diff: 91.94%)  
  
> Merging [#152](https://codecov.io/gh/vinniefalco/Beast/pull/152?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **1.60%**  
  
``` diff  
@@             master       #152   diff @@  
==========================================  
  Files            71         80      +9     
  Lines          4193       5569   +1376     
  Methods           0          0             
  Messages          0          0             
  Branches          0          0             
==========================================  
+ Hits           4091       5344   +1253     
- Misses          102        225    +123     
  Partials          0          0             
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [7b4de4b...1b6a386](https://codecov.io/gh/vinniefalco/Beast/compare/7b4de4b9513105845181321ba672bea4ce4a9062...1b6a386d2123bb2d391d55b0d6714e17f852b432?src=pr)

---

## Comment 3

> Username: seelabs  
> Created_at: 2016-11-01 01:51:20 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-257471025  

Great work on this. Lots of unneeded code went away, macros removed, reduced memory allocations, added enums & classes, and added error codes. All of those "little" changes add up to a vastly improved code base.  
  
I reviewed this by reviewing the many commits on the on zlib-19 branch and confirming the transformations did not introduce errors. I could not find any problems. However, there were a _lot_ of changes and some of the changes were hard to review (things that moved lots of code around). My biggest recommendation is to add testing against the "standard" zlib library: for example: compress with that zlib library and decompress with the beast zlib.  
  
I did not look for nits (it would take too long to comb through such a large PR for nits), however a few did pop out while I reviewed. Here's a commit with couple of nits I found: https://github.com/seelabs/Beast/commit/797778d962fe5a02a2cb68608f438b84992d2c67  
  
Note: CI reports a warning from gcc (I don't see this warning locally):  
`deflate_stream.hpp:1048:10: warning: ‘*((void*)& old_flush +4)’ may be used uninitialized in this function [-Wmaybe-uninitialized]` Warning looks like a false alarm to me, not sure what to do about that one.  
  
:+1:

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-11-01 02:01:50 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-257472319  

> add testing against the "standard" zlib library: for example: compress with that zlib library and decompress with the beast zlib.  
  
You mean like this?  
https://github.com/vinniefalco/Beast/blob/zlib/test/zlib/inflate_stream.cpp#L311

---

## Comment 5

> Username: seelabs  
> Created_at: 2016-11-01 02:05:48 UTC  
> Updated_at: 2016-11-01 02:06:19 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-257472820  

@vinniefalco Ug, yes. (embarrassed).

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-11-01 02:06:38 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-257472942  

> Note: CI reports a warning from gcc (I don't see this warning locally):  
  
This is something to do with `boost::optional`. I have shuffled it around a few times to make the warning go away, to no avail. I don't like the warning, I'd like to get rid of it. Maybe we can analyze it and use a bool + int instead of optional.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-11-01 12:25:56 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-257554021  

> I did not look for nits  
  
Well the original code is like one giant nit

---

## Comment 8

> Username: JoelKatz  
> Created_at: 2016-11-04 17:10:15 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-258491154  

👍 This is awesome.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-11-04 17:34:44 UTC  
> Url: https://github.com/boostorg/beast/pull/152#issuecomment-258497742  

Part of #168

---
