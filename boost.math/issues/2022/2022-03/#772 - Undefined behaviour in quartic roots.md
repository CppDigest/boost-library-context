# #772 - Undefined behaviour in quartic roots [Closed]

> Username: jzmaddock  
> Created at: 2022-03-08 15:40:41 UTC  
> Updated at: 2022-03-08 16:01:59 UTC  
> Closed at: 2022-03-08 16:01:58 UTC  
> Url: https://github.com/boostorg/math/issues/772  

Just a reminder, mostly for @NAThompson that the quartic roots code still has UB as it deliberately sorts a list containing NaN's, this triggers an exception inside the std lib for latest msvc.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-03-08 16:01:58 UTC  
> Url: https://github.com/boostorg/math/issues/772#issuecomment-1061937304  

My bad, I see you've already fixed this!
