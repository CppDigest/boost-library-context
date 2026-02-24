# #17 Cleanup & Serialization [Merged]

> Username: klemens-morgenstern  
> Created at: 2016-06-18 17:17:08 UTC  
> Updated at: 2016-06-20 16:56:55 UTC  
> Merged at: 2016-06-20 16:56:55 UTC  
> Closed at: 2016-06-20 16:56:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/17  

Hi Hans,  
  
I added comments with copyright and moved the serialization to a custom header and added a compile test for the latter (b2 only). I also removed all unneeded macros (was quite unreadable with the locally declared ones).  
  
It now has a new header (boost/histogram/serialization) with the serialization defs.  
Also boost/histogram.hpp was added for convience, i..e it include histogram.hpp and serialization.hpp.  
  
Lastly, I changed fill_c and wfill_c to fill(boost::iterator_range). Passing to iterators would've been an invalid ambiguity (when passing int), so now it takes a range. Should be fine, since it's already a boost library.  
  
LG,  
  
Klemens

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-06-18 18:22:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/17#issuecomment-226957427  

Ok, I also changed the size check in histogram to std::range_error. It is not a critical error, hence it should be able to handle it; the assert would cause the program to crash immediatly.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2016-06-18 18:40:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/17#issuecomment-226958383  

Ouh, some doc changes moved in here accidentily - I'll remove them if you want to pull. I'll open another pull request for doc stuff that later.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-06-18 18:48:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/17#issuecomment-226958726  

Alright, tests pass as in your master branch - the fifth python test errors for some reason unknown to me...

---

## Comment 4

> Username: HDembinski  
> Created_at: 2016-06-20 16:56:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/17#issuecomment-227202295  

we have to work a bit on the details (see email discussion), but i am going to include this to go forward. thanks :) !

---
