# #215 Iso doc fixes [Merged]

> Username: lukem  
> Created at: 2022-08-07 02:45:08 UTC  
> Updated at: 2022-09-22 01:56:45 UTC  
> Merged at: 2022-09-20 22:10:38 UTC  
> Closed at: 2022-09-20 22:10:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/215  

I've made a few fixes to the Boost.Date_Time documentation (including comments):  
  
1. The fractional separator has always been implemented (back to 2002) as ".", but the documentation sometimes used ",". I've fixed the documentation  
2. Consistently use "ISO 8601" to refer to the standard, instead of just "iso" or "ISO".  
3. Consistently document that fractional seconds are only included if non-zero. This fixes Issue #110.

---

## Comment 1

> Username: JeffGarland  
> Created_at: 2022-09-20 22:10:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/215#issuecomment-1252967812  

This looks good to me -- thanks!

---
