# #257 Add test for some bit-aligned pixel types and metafunctions [Merged]

> Username: mloskot  
> Created at: 2019-03-12 22:12:18 UTC  
> Updated at: 2019-03-29 20:26:17 UTC  
> Merged at: 2019-03-13 18:15:34 UTC  
> Closed at: 2019-03-13 18:15:34 UTC  
> Url: https://github.com/boostorg/gil/pull/257  

Test `bit_aligned_pixel_reference` and `packed_pixel` as well as some of related metafunctions generating types of their members and intermediate specializations.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
The new tests are part of MPL to MP11 verification. The uses of `boost::mpl` in the new tests will switch to `boost::mp11`.

---
