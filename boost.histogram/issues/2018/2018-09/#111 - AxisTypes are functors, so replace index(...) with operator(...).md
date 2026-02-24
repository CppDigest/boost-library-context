# #111 - AxisTypes are functors, so replace index(...) with operator(...) [Closed]

> Username: HDembinski  
> Created at: 2018-09-27 09:02:04 UTC  
> Updated at: 2018-10-27 17:49:08 UTC  
> Closed at: 2018-10-27 17:49:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/111  

Replace `index(...)` method with `operator(...)`. AxisTypes are conceptionally functors that map values to indices.
