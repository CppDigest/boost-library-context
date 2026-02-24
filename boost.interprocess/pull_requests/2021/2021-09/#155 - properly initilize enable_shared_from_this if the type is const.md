# #155 properly initilize enable_shared_from_this if the type is const [Merged]

> Username: apolukhin  
> Created at: 2021-09-24 11:28:09 UTC  
> Updated at: 2021-09-24 16:39:08 UTC  
> Merged at: 2021-09-24 16:30:47 UTC  
> Closed at: 2021-09-24 16:30:47 UTC  
> Url: https://github.com/boostorg/interprocess/pull/155  

Without `const` the new test fails with the following error:  
```  
terminate called after throwing an instance of 'boost::interprocess::bad_weak_ptr'  
  what():  boost::interprocess::bad_weak_ptr  
Aborted (core dumped)  
```

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-09-24 16:30:59 UTC  
> Url: https://github.com/boostorg/interprocess/pull/155#issuecomment-926765916  

Many thanks for the patch!

---
