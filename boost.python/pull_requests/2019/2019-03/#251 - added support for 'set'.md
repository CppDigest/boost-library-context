# #251  added support for 'set' [Open]

> Username: FadyEssam  
> Created at: 2019-03-03 01:07:31 UTC  
> Updated at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/251  



---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-03-03 03:06:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/251#pullrequestreview-209892576  

📁 include/boost/python/set.hpp

```diff
   6 |+ 
   7 |+ namespace boost {
   8 |+ 	namespace python {
```

> Username: stefanseefeld  
> Created_at: 2019-03-03 03:06:11 UTC  
> Updated_at: 2019-03-03 15:49:14 UTC  
> Url: https://github.com/boostorg/python/pull/251#discussion_r261849880  

please reformat the patch to use two spaces as indentation, rather than eight.

> Username: FadyEssam  
> Created_at: 2019-03-03 10:10:25 UTC  
> Updated_at: 2019-03-03 15:49:14 UTC  
> Url: https://github.com/boostorg/python/pull/251#discussion_r261860126  

I think all the mentioned issues are resolved now


---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-03-03 03:17:32 UTC  
> Url: https://github.com/boostorg/python/pull/251#issuecomment-468983544  

Given the similarities with other Python types, perhaps you can guess what build logic needs to be added ? (Have a look at `build/Jamfile`).  
Similarly, perhaps you could clone some of the logic from `test/dict.*` to come up with a test for `set` ?

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2019-03-03 14:27:31 UTC  
> Url: https://github.com/boostorg/python/pull/251#issuecomment-469028330  

There are still a few pieces of build infrastructure missing:  
* add the new source file to https://github.com/boostorg/python/blob/develop/src/fabscript,,,  
* ...as well as https://github.com/boostorg/python/blob/develop/build/Jamfile  
* Add the test to https://github.com/boostorg/python/blob/develop/test/fabscript

---

## Comment 4

> Username: FadyEssam  
> Created_at: 2019-03-03 15:50:31 UTC  
> Url: https://github.com/boostorg/python/pull/251#issuecomment-469035476  

done, Thanks for the guidance

---

## Review 5 [Commented]

> Username: lightmare  
> Created_at: 2019-12-05 15:40:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/251#pullrequestreview-327645626  

📁 src/set.cpp

```diff
  49 |+ 		else
  50 |+ 		{
  51 |+ 		  this->attr("discrad")(x);
```

> Username: lightmare  
> Created_at: 2019-12-05 15:40:13 UTC  
> Updated_at: 2019-12-05 15:40:14 UTC  
> Url: https://github.com/boostorg/python/pull/251#discussion_r354386497  

there's a typo here


---
