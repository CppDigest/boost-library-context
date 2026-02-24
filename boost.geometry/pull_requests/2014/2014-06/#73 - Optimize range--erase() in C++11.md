# #73 [range] Optimize range::erase() in C++11 [Merged]

> Username: awulkiew  
> Created at: 2014-06-21 20:42:27 UTC  
> Updated at: 2014-06-22 13:31:44 UTC  
> Merged at: 2014-06-22 13:27:08 UTC  
> Closed at: 2014-06-22 13:27:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/73  



---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-21 21:03:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/73#issuecomment-46764717  

What's the current guideline regarding the use of boost::enable_if? AFAIR we already use it in the iterators code. I'm considering implementing copy_or_move() using this Core utility.  
Could we use it or should prefer tag dispatching/specializations in all cases?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-06-22 09:09:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/73#issuecomment-46776006  

The guideline is clear: avoid all enable_if, please use specializations everywhere

---
