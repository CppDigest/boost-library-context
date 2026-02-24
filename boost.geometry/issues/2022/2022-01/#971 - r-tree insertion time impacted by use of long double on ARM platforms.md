# #971 - r-tree insertion time impacted by use of long double on ARM platforms [Closed]

> Username: mikexing2018  
> Created at: 2022-01-24 23:56:48 UTC  
> Updated at: 2022-02-23 10:31:27 UTC  
> Closed at: 2022-02-23 10:30:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/971  

Wanted to open this more as a discussion and maybe possible enhancement. Apologize if there is a better place to start this discussion. This was the only place I know.  
  
Background:   
I found that the r-tree insert() function performs much slower on ARM platform due to its internal use of long doubles. On ARM platform, long doubles are 128bits and ARM do not offer hardware support for 128bit floating operations. As a result, Soft-float-library-routines are used by the compiler, which greatly reduces the performance of insert().  
  
Question:  
Why do we use long double? Is the extra precision required or does using double suffice?  
  
Places where long double is used:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/algorithms/content.hpp#L32  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/algorithms/margin.hpp#L35

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-01-25 11:24:40 UTC  
> Updated at: 2022-01-25 11:25:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/971#issuecomment-1021088995  

I can't remember the reasoning and indeed it seems to be incorrect. The only idea I have is that it's because of precision of the result. But we don't really need the precision here. `double` would be enough. It is probably only important for integral coordinate types anyway. But even `float` would probably be enough for 32bit and lesser coordinate types (incl. integral) in this case.  
  
The results of these algorithms are used during balancing to decide which node should contain an element. For the inaccuracies to cause problems the nodes would have to be fairly equal anyway, or the coordinates would have to be big so floating points were unable to represent them accurately. So in practice using smaller floating point numbers probably wouldn't be a problem.  
  
Would `double` be ok for your use case or would you prefer going even further and e.g. use `float` for `float` coordinate types?

---

## Comment 2

> Username: mikexing2018  
> Created at: 2022-01-27 18:26:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/971#issuecomment-1023519268  

double should be ok for our use case.  
  
The story is that we actually ended up using packing algorithm and that has been the workaround to get the speed up that we needed. We don't have all the data beforehand, but we would use a vector to store all the data and when new data arrives, we would use the new vector to reconstruct the r-tree. Even though we would construct r-tree with all the data each time, it's still faster than using insert() on the incremental new data.
