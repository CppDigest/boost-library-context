# #326 Tidy up headers in gil/image_processing/ directory [Merged]

> Username: mloskot  
> Created at: 2019-07-08 22:38:09 UTC  
> Updated at: 2020-02-22 13:13:00 UTC  
> Merged at: 2019-07-09 08:01:43 UTC  
> Closed at: 2019-07-09 08:01:43 UTC  
> Url: https://github.com/boostorg/gil/pull/326  

Add missing `inline` specifier for tiny functions.  
Add missing include guards.  
Add missing copyright notice.  
Remove superfluous `boost::gil::` namespace qualifiers.  
Make use of 100 characters line length limit:  
 - Remove superfluous newlines to avoid too much of vertical stretch.  
 - Tidy up Doxygen comments.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
  
------  
  
FYI @simmplecoder

---
