# #5 Fix cast to prevent -Wconversion warning in g++. [Closed]

> Username: mbradle  
> Created at: 2015-02-22 13:45:31 UTC  
> Updated at: 2015-02-23 12:39:18 UTC  
> Closed at: 2015-02-23 12:39:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/5  

This change fixes a -Wconversion warning in g++ version 4.9.2 on mac yosemite.  For example, in multi_index/example, I run  
  
_g++ -Wconversion -I../../.. -o hashed hashed.cpp_  
  
and get the warning.  With the change, I do not get the warning.  I'm guessing that the compiler is being picky since bucket_count() is unsigned.  mlf is a float, so I think this still has the intended effect.

---
