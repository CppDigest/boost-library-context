# #16 Remove unused deprecated includes [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-14 09:00:45 UTC  
> Updated at: 2015-09-14 12:37:19 UTC  
> Merged at: 2015-09-14 12:08:53 UTC  
> Closed at: 2015-09-14 12:08:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/16  

A comment in boost/iterator.hpp and boost/detail/iterator.hpp mentions that  
the files are obsolete and will be deprecated. All they do is pull some types  
from namespace std into namespace boost.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-14 12:19:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/16#issuecomment-140053519  

I had to revert your pull request after testing locally showed problems.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-09-14 12:22:21 UTC  
> Url: https://github.com/boostorg/iterator/pull/16#issuecomment-140053879  

Oh, sorry! Everything worked for me on MSVC 14. I'll take another look.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-09-14 12:36:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/16#issuecomment-140056215  

It is as much my fault for not testing locally before I did the merge.  
  
Look at boost/pending/iterator_tests.hpp.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2015-09-14 12:37:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/16#issuecomment-140056336  

Sorry, looks like I forgot to push the changes in include/boost/pending/iterator_tests.hpp :-( Now all unit tests are passing on that branch.

---
