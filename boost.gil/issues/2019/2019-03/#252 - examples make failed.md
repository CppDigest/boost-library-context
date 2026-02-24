# #252 - examples make failed [Closed]

> Username: sid19991  
> Created at: 2019-03-06 16:47:50 UTC  
> Updated at: 2019-03-06 18:09:56 UTC  
> Closed at: 2019-03-06 17:17:42 UTC  
> Url: https://github.com/boostorg/gil/issues/252  

I'm getting error like this  
  
 error: ‘rgb8_image_t’ is not a member of ‘bg’  
  
when building examples from makefile in boost.gil.

---

## Comment 1

> Username: sid19991  
> Created at: 2019-03-06 17:17:42 UTC  
> Url: https://github.com/boostorg/gil/issues/252#issuecomment-470195266  

got the error: i just have to specify -std=c++11 in makefile

---

## Comment 2

> Username: mloskot  
> Created at: 2019-03-06 18:09:56 UTC  
> Url: https://github.com/boostorg/gil/issues/252#issuecomment-470214290  

FYI, https://github.com/boostorg/gil/commit/9004a676625a48aea96e86b31b9dd4198a779fd9  
```  
Remove old example/Makefile  
  
It is not maintained; may be broken; redundant.  
```
