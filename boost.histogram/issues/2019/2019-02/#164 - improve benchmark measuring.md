# #164 - improve benchmark measuring [Closed]

> Username: HDembinski  
> Created at: 2019-02-23 20:59:51 UTC  
> Updated at: 2019-05-04 05:45:57 UTC  
> Closed at: 2019-05-04 05:45:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/164  

B. Reese: "Looking at the Boost.Histogram benchmark code, I suggest that you insert sequentially consistent member fences between reading the clock and executing your code"  
  
https://en.cppreference.com/w/cpp/atomic/atomic_thread_fence

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-05-04 05:45:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/164#issuecomment-489296709  

fixed in develop
