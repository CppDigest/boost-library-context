# #76 Improve casts in channel_convert_from_unsigned (refines PR #74) [Merged]

> Username: mloskot  
> Created at: 2018-04-08 16:01:03 UTC  
> Updated at: 2018-04-08 16:10:15 UTC  
> Merged at: 2018-04-08 16:05:12 UTC  
> Closed at: 2018-04-08 16:05:12 UTC  
> Url: https://github.com/boostorg/gil/pull/76  

### Description  
  
Add cast to explicitly indicate `val` promotion to at least 32-bit integer.  
  
The arithmetic operations are performed on `int` of higher rank type.  
Finally, since the result value is guaranteed to fit range of `8/16-bit` result integer type, it is safe to cast.  
This should make it clearer for a reader that subtleties of implicit promotions and conversions have been considered.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### References (eg. other issues, pull requests)  
  
Refines #74

---
