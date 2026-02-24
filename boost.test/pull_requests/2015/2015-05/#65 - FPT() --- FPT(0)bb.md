# #65 FPT() --> FPT(0)bb [Merged]

> Username: akrzemi1  
> Created at: 2015-05-25 20:16:39 UTC  
> Updated at: 2015-05-25 20:18:37 UTC  
> Merged at: 2015-05-25 20:18:37 UTC  
> Closed at: 2015-05-25 20:18:37 UTC  
> Url: https://github.com/boostorg/test/pull/65  

Changed the occurences of FPT() to FPT(0). Since we expect a construction from int anyway,  
and since the previous implementation assumed that the default constructor initializes to  
numeric vaue zero, the change appears uncontroversial. What we gain, is that the default  
ctor is no longer required. The FP algos work for non-default-constructible types.

---
