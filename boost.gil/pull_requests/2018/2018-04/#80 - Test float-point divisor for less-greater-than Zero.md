# #80 Test float-point divisor for less/greater-than Zero [Merged]

> Username: mloskot  
> Created at: 2018-04-10 10:24:47 UTC  
> Updated at: 2018-04-10 12:53:31 UTC  
> Merged at: 2018-04-10 12:53:27 UTC  
> Closed at: 2018-04-10 12:53:27 UTC  
> Url: https://github.com/boostorg/gil/pull/80  

### Description  
  
Add divisor check to `point2<T>::operator/=`, if Zero do nothing.  
  
This **hack** helps to avoid compiler warnings without polluting the source with pragmas.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
