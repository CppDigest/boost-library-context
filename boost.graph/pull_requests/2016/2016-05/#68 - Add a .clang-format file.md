# #68 Add a .clang-format file. [Merged]

> Username: murraycu  
> Created at: 2016-05-04 08:33:04 UTC  
> Updated at: 2016-10-31 19:21:10 UTC  
> Merged at: 2016-10-31 19:21:10 UTC  
> Closed at: 2016-10-31 19:21:10 UTC  
> Url: https://github.com/boostorg/graph/pull/68  

So we can at least make the examples use a more consistent code style.  
  
Run clang-format like so:  
$ clang-format -i *.cpp  
and use git diff to see what has changed.  
  
This will strip trailing whitespace too. You can ignore that by  
using git diff -w  
  
<!-- Reviewable:start -->  
  
---  
  
This change is [<img src="https://reviewable.io/review_button.svg" height="35" align="absmiddle" alt="Reviewable"/>](https://reviewable.io/reviews/boostorg/graph/68)  
  
<!-- Reviewable:end -->

---

## Comment 1

> Username: murraycu  
> Created_at: 2016-05-04 08:35:24 UTC  
> Url: https://github.com/boostorg/graph/pull/68#issuecomment-216786237  

This is just an initial suggestion. I don't see a very consistent coding style in Boost Graph, and I cannot find any general Boost C++ coding guidelines.  
  
We should remove "SpacesInAngles: true" when BGL requires C++11.

---

## Comment 2

> Username: maelvls  
> Created_at: 2016-05-04 09:00:51 UTC  
> Url: https://github.com/boostorg/graph/pull/68#issuecomment-216799095  

I just tested `clang-format`, I didn't know of that! Nice idea!

---
