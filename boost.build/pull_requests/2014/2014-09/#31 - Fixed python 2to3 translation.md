# #31 Fixed python 2to3 translation [Merged]

> Username: dean0x7d  
> Created at: 2014-09-01 21:54:52 UTC  
> Updated at: 2021-10-02 22:35:47 UTC  
> Merged at: 2014-09-02 10:08:31 UTC  
> Closed at: 2014-09-02 10:08:31 UTC  
> Url: https://github.com/boostorg/build/pull/31  

I noticed that the Boost.Python tests would not run with Python 3.x. This fixes the issue.  
I also added "--no-diffs" to silence the 2to3 diff output which is too verbose by default.

---
