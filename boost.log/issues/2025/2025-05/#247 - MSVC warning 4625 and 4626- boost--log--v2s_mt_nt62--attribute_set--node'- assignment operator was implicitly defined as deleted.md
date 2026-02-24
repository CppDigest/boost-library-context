# #247 - MSVC warning 4625 and 4626: boost::log::v2s_mt_nt62::attribute_set::node': assignment operator was implicitly defined as deleted [Closed]

> Username: ahall-kpm  
> Created at: 2025-05-14 14:04:17 UTC  
> Updated at: 2025-05-14 14:41:04 UTC  
> Closed at: 2025-05-14 14:37:51 UTC  
> Url: https://github.com/boostorg/log/issues/247  

This warning forcing me to wrap seemingly random code in `#pragma warning (disable 4625 4626)`. It can be solved by explicitly deleting these operators.  
  
I will submit a PR to resolve this issue.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-05-14 14:38:11 UTC  
> Url: https://github.com/boostorg/log/issues/247#issuecomment-2880496556  

Thanks, I disabled the warnings globally.

---

## Comment 2

> Username: ahall-kpm  
> Created at: 2025-05-14 14:41:03 UTC  
> Url: https://github.com/boostorg/log/issues/247#issuecomment-2880506122  

Thanks, appreciated.
