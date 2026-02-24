# #735 - Small bug in karma real_generator output. [Closed]

> Username: IlyaKiparenko  
> Created at: 2022-09-15 09:01:04 UTC  
> Updated at: 2022-09-22 21:05:17 UTC  
> Closed at: 2022-09-22 21:05:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/735  

If I try to output number 0.099999999999999770 (double) with 16 digits precision, boost karma generates wrong output of "0.9999999999999999" which is 10 times larger. As far as I can tell this is an issue of log10 rounding, which for this number produces 15.0 exactly instead of 14.999.... in real_policies.hpp : fraction_part. This causes karma to not output a leading zero.  
  
I'm not exactly sure, but I think this issue can be resolved by replacing floor(log10(n)) + 1 with ceil(log10(n)). Also there is probably other places in real generator where such issues can arise.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-16 02:58:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/735#issuecomment-1248861465  

Looks exactly like #529  
  
> I'm not exactly sure, but I think this issue can be resolved by replacing floor(log10(n)) + 1 with ceil(log10(n)).  
  
That won't work with powers of ten. `ceil(log10(n + 1)))` will.  
  
> Also there is probably other places in real generator where such issues can arise.  
  
Or they are already workarounded in #629.
