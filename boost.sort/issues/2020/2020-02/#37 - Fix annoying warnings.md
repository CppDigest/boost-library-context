# #37 - Fix annoying warnings [Closed]

> Username: cdcseacave  
> Created at: 2020-02-06 11:12:33 UTC  
> Updated at: 2020-02-20 03:11:49 UTC  
> Closed at: 2020-02-20 03:11:49 UTC  
> Url: https://github.com/boostorg/sort/issues/37  

Conversion from int64 to int in header file `boost\sort\pdqsort\pdqsort.hpp`, reported as warning per few screen and for almost every CPP in my project:  
- line 130  
```  
                limit += (int)(cur - sift);  
```  
- line 265  
```  
        int unknown_left = (int)(last - first) - ((num_r || num_l) ? block_size : 0);  
```  
And in file `boost\iostreams\filter\zstd.hpp`:  
- line 94  
```  
    int error() const { return (int)error_; }  
```

---

## Comment 1

> Username: spreadsort  
> Created at: 2020-02-09 02:30:02 UTC  
> Url: https://github.com/boostorg/sort/issues/37#issuecomment-583797654  

I've asked orsonpeters@gmail.com (the author) to follow up on this.

---

## Comment 2

> Username: spreadsort  
> Created at: 2020-02-20 03:11:49 UTC  
> Url: https://github.com/boostorg/sort/issues/37#issuecomment-588587417  

I've merged Orson's fix.
