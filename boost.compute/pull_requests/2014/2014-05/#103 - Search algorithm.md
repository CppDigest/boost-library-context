# #103 Search algorithm [Merged]

> Username: roshanrags  
> Created at: 2014-05-05 06:03:47 UTC  
> Updated at: 2014-05-08 09:48:15 UTC  
> Merged at: 2014-05-08 04:04:40 UTC  
> Closed at: 2014-05-08 04:04:40 UTC  
> Url: https://github.com/boostorg/compute/pull/103  

Added pattern matching kernel which will form the base for most of the search algorithms. Also added `search` algorithm and test.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-05 06:22:31 UTC  
> Url: https://github.com/boostorg/compute/pull/103#issuecomment-42161747  

[![Coverage Status](https://coveralls.io/builds/738445/badge)](https://coveralls.io/builds/738445)  
  
Coverage remained the same when pulling **747fe2d41f6b32bbaba9f97d2ea307d3d0dfe952 on roshanr95:search_algorithms** into **d23894322f28573115fbf8aa17355610c348208a on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-05-06 01:19:26 UTC  
> Updated_at: 2014-05-07 10:45:53 UTC  
> Url: https://github.com/boostorg/compute/pull/103#discussion_r12308117  

unused variable?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-05-06 01:21:03 UTC  
> Updated_at: 2014-05-07 10:45:53 UTC  
> Url: https://github.com/boostorg/compute/pull/103#discussion_r12308147  

can you update this to use the three forward slash doxygen style (`///`)? that's what the rest of the code uses and it's easier to search/grep for.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-05-06 01:25:59 UTC  
> Updated_at: 2014-05-07 10:45:53 UTC  
> Url: https://github.com/boostorg/compute/pull/103#discussion_r12308219  

there's a shortcut for this. you can just do `bc::vector<bc::int_> vectorp(datap, datap + 2, queue)` which both allocates the exact amount of space required and copies the data to the device.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-05-06 01:28:39 UTC  
> Url: https://github.com/boostorg/compute/pull/103#issuecomment-42259403  

Looking good!  
  
Left a few inline comments. Also, I noticed some trailing whitespace in the new code. Can you check your editor settings?

---

## Comment 6

> Username: roshanrags  
> Created_at: 2014-05-06 08:34:44 UTC  
> Updated_at: 2014-05-07 10:45:53 UTC  
> Url: https://github.com/boostorg/compute/pull/103#discussion_r12314787  

Actually part of an improvement I am thinking of - first eliminating some indices from which the pattern cannot start and then applying this on a set of "suspect" indices. But definitely unused for now. Will remove it.

---

## Comment 7

> Username: roshanrags  
> Created_at: 2014-05-06 08:35:16 UTC  
> Updated_at: 2014-05-07 10:45:53 UTC  
> Url: https://github.com/boostorg/compute/pull/103#discussion_r12314801  

+1, thanks ...

---

## Comment 8

> Username: roshanrags  
> Created_at: 2014-05-07 11:07:29 UTC  
> Url: https://github.com/boostorg/compute/pull/103#issuecomment-42414322  

Fixed!!

---

## Comment 9

> Username: coveralls  
> Created_at: 2014-05-07 11:08:56 UTC  
> Url: https://github.com/boostorg/compute/pull/103#issuecomment-42414434  

[![Coverage Status](https://coveralls.io/builds/746273/badge)](https://coveralls.io/builds/746273)  
  
Coverage remained the same when pulling **79d5353a4e536d03e3983162b777002278688cfb on roshanr95:search_algorithms** into **d23894322f28573115fbf8aa17355610c348208a on kylelutz:develop**.

---

## Comment 10

> Username: kylelutz  
> Created_at: 2014-05-08 04:04:43 UTC  
> Url: https://github.com/boostorg/compute/pull/103#issuecomment-42511456  

Merged!

---
