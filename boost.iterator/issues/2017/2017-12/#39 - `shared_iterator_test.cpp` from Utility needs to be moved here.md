# #39 - `shared_iterator_test.cpp` from Utility needs to be moved here [Closed]

> Username: pdimov  
> Created at: 2017-12-23 01:34:44 UTC  
> Updated at: 2017-12-24 12:25:57 UTC  
> Closed at: 2017-12-24 12:25:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/39  

It looks like we've moved `boost/shared_container_iterator.hpp` here, but forgot the test.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-23 03:31:42 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353704493  

It is only 15 years after the fact that you discovered this <g>. I agree we need some tests.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-23 03:37:24 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353704676  

Time flies, doesn't it?  
  
Further careful inspection w/ glasses on reveals three example files and a documentation file, all sitting in `libs/utility` waiting to be awakened from their 15 year slumber.

---

## Comment 3

> Username: eldiener  
> Created at: 2017-12-23 03:42:07 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353704838  

I had a smiley in the first line of my previous reply but somehow it was removed. In the docs I can see three examples also, as well as documentation already existing for shared_container_iterator in the iterator docs.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-12-23 03:51:46 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353705121  

The smiley did come across through e-mail, so all's not lost.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-12-23 03:55:01 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353705250  

The docs have apparently been ported to .qbk here, with the old .html still left there; the .qbk links the examples from libs/utility. :-)

---

## Comment 6

> Username: pdimov  
> Created at: 2017-12-23 04:27:03 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353706263  

> I agree we need some tests.  
  
On reflection, I'm not sure if you misinterpreted what I mean here; I'm saying that there's already a test, but it's in `libs/utility/test`, and just needs to be brought in (per the issue title).

---

## Comment 7

> Username: eldiener  
> Created at: 2017-12-23 05:25:37 UTC  
> Updated at: 2017-12-23 05:26:12 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353708122  

Do you really just want to remove the shared_iterator_test.cpp from 'utility' and add it to 'iterator' ? It uses 'assert' as its checking mechanism. That is not what I would call ideal. At least it could use lightweight_test.hpp to do its checking. Also we probably want to at least add the examples, which are part of the 'iterator' documentation for shared_iterator, as simple tests also.

---

## Comment 8

> Username: pdimov  
> Created at: 2017-12-24 12:25:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/39#issuecomment-353781522  

Done in https://github.com/boostorg/iterator/commit/218dc4baf173d09871757c9278e7b4eb4f9a3f52.
