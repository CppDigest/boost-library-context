# #602 - `mp_radix_size` pointer type incorrect in some versions of tommath [Closed]

> Username: mborland  
> Created at: 2024-02-28 09:49:08 UTC  
> Updated at: 2024-03-04 09:39:29 UTC  
> Closed at: 2024-03-04 09:39:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/602  

From the users ML:  
  
I tried to use boost muiltiprecision with Visual Studio 2022, 64 bit and got the following error message:  
  
boost\multiprecision\tommath.hpp(444,36): error C2664: 'mp_err mp_radix_size(const mp_int *,int,size_t *)': cannot convert argument 3 from 'int *' to 'size_t *'  
  
I used tommath.h from  
  
https://github.com/libtom/libtommath/blob/develop/tommath.h  
  
that has (line 583)  
  
      mp_err mp_radix_size(const mp_int *a, int radix, size_t *size) MP_WUR;  
  
but boost\multiprecision\tommath.hpp, lineS 443/444 are  
  
      int s;  
      detail::check_tommath_result(mp_radix_size(const_cast< ::mp_int*>(&m_data), base, &s));  
  
I compared with cygwin, there tommath.h is  
  
      mp_err mp_radix_size(const mp_int *a, int radix, int *size) MP_WUR;  
  
So I am not sure: do I need some different tommath for Visual Studio 2022, 64 bit, or should boost\multiprecision\tommath.hpp be changed?  
  
Helmut

---

## Comment 1

> Username: mborland  
> Created at: 2024-02-28 10:21:35 UTC  
> Updated at: 2024-02-28 10:24:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/602#issuecomment-1968669774  

Change was made: https://github.com/libtom/libtommath/commit/a29aa59baa529d341e2b234ff7331e827aba1ffb  
  
Which years ago, but doesn't seem to have ever made release.
