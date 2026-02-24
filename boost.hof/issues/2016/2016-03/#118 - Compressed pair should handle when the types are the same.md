# #118 - Compressed pair should handle when the types are the same [Closed]

> Username: pfultz2  
> Created at: 2016-03-05 17:11:21 UTC  
> Updated at: 2016-03-23 18:32:48 UTC  
> Closed at: 2016-03-22 23:52:52 UTC  
> Url: https://github.com/boostorg/hof/issues/118  

Here are some test cases:  
  
``` cpp  
fit::by(fit::identity, fit::identity)(0);  
fit::capture(fit::identity)(identity)();  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-21 23:31:27 UTC  
> Url: https://github.com/boostorg/hof/issues/118#issuecomment-199535873  

Of course, this is not a problem with `compressed_pair`, its the fact that the `by` adaptor doesn't use `compressed_pair`.
