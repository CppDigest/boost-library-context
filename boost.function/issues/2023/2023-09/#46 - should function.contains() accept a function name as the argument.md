# #46 - should function.contains() accept a function name as the argument? [Closed]

> Username: fmhess  
> Created at: 2023-09-01 20:22:06 UTC  
> Updated at: 2023-09-03 17:23:26 UTC  
> Closed at: 2023-09-03 17:23:25 UTC  
> Url: https://github.com/boostorg/function/issues/46  

The following doesn't work (it does if you pass &foo to the contains method).  Should it?  There is a slight asymmetry since the constructor does accept a function name.  
  
```  
void foo() { }  
  
boost::function<void ()> func(foo);  
func.contains(foo);  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-09-01 21:13:48 UTC  
> Url: https://github.com/boostorg/function/issues/46#issuecomment-1703325680  

I think that it should (be made to) work.
