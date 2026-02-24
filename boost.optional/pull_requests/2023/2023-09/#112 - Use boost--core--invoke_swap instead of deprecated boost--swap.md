# #112 Use boost::core::invoke_swap instead of deprecated boost::swap [Closed]

> Username: Flamefire  
> Created at: 2023-09-11 07:28:53 UTC  
> Updated at: 2023-10-07 16:18:56 UTC  
> Closed at: 2023-10-01 09:37:52 UTC  
> Url: https://github.com/boostorg/optional/pull/112  

This caused warnings and partially errors during build and in dependent libs.  
  
This also fixes the failed GHA CI by not using the removed ubuntu-18 runner images anymore

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2023-10-01 09:37:52 UTC  
> Url: https://github.com/boostorg/optional/pull/112#issuecomment-1742023038  

This has been fixed by merging a duplicate PR: https://github.com/boostorg/optional/pull/110

---
