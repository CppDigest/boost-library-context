# #331 - rgba.hpp: introduced missing RView type alias in version 1.70 [Closed]

> Username: deh00  
> Created at: 2019-07-11 18:47:36 UTC  
> Updated at: 2019-07-12 11:32:42 UTC  
> Closed at: 2019-07-12 11:32:18 UTC  
> Url: https://github.com/boostorg/gil/issues/331  

rgba.hpp: typo introduced when changing RView typedef to type alias in version 1.70.  
see line 46  
https://www.boost.org/doc/libs/1_69_0/boost/gil/rgba.hpp  
https://www.boost.org/doc/libs/1_70_0/boost/gil/rgba.hpp

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-11 19:02:49 UTC  
> Updated at: 2019-07-11 20:58:08 UTC  
> Url: https://github.com/boostorg/gil/issues/331#issuecomment-510613572  

Thank you for reporting this.  
  
I think you mean this bit, to be exact   
  
https://github.com/boostorg/gil/blob/5611bd58071873d6346fe3cc05fe86c72f697717/include/boost/gil/rgba.hpp#L49-L51  
  
Apparently, we need tests to cover that bit of the code!  
  
```  
include\boost\gil\rgba.hpp(53): error C3861: 'RView': identifier not found  
```

---

## Comment 2

> Username: mloskot  
> Created at: 2019-07-12 11:32:42 UTC  
> Url: https://github.com/boostorg/gil/issues/331#issuecomment-510853321  

It's been fixed in `develop`.
