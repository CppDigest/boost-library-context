# #141 - compiler warning on 64bits gcc [Open]

> Username: GregKon  
> Created at: 2020-04-22 08:23:28 UTC  
> Updated at: 2022-10-30 00:13:33 UTC  
> Url: https://github.com/boostorg/type_traits/issues/141  

Hi  
  
File is_complete.hpp make warning in line 47:  
  
      template <unsigned N>  
      struct ok_tag { double d; char c[N]; };  
      template <class T>  
      ok_tag<sizeof(T)> check_is_complete(int);  
  
specialization ok_tag<sizeof(T)> check_is_complete(int); make implicit cast from unsigned long long (size_t is unsigned long long on 64 arch) to unsigned (32bits) and ugly warning on output.  
It may be easy overcome and make more portable by:  
  
      template <size_t N>  
      struct ok_tag { double d; char c[N]; };

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-30 00:13:33 UTC  
> Url: https://github.com/boostorg/type_traits/issues/141#issuecomment-1296032199  

This seems to have been fixed by #163.
