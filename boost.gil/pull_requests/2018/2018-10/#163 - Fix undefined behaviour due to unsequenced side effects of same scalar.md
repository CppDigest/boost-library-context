# #163 Fix undefined behaviour due to unsequenced side effects of same scalar [Merged]

> Username: mloskot  
> Created at: 2018-10-24 21:39:05 UTC  
> Updated at: 2018-10-30 17:19:09 UTC  
> Merged at: 2018-10-25 11:38:23 UTC  
> Closed at: 2018-10-25 11:38:23 UTC  
> Url: https://github.com/boostorg/gil/pull/163  

Let's give toolbox some love.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
----  
  
By the way, apart from the expression nonsense, there is also testing `uint8_t` for values of `256`   
  
```  
toolbox/test/indexed_image_test.cpp: In lambda function:  
toolbox/test/indexed_image_test.cpp:40:36: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
                             i = (i == 256) ? 0 : (i + 1);  
                                    ^  
toolbox/test/indexed_image_test.cpp: In lambda function:  
toolbox/test/indexed_image_test.cpp:51:36: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
                             i = (i == 256) ? 0 : (i + 1);  
                                    ^  
```  
  
Now, is the `uint8_t` supposed to be larger or `256` decreased to `255`?

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-10-25 11:28:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/163#pullrequestreview-168332826  

Oh, wow ! Excellent catch !

---
