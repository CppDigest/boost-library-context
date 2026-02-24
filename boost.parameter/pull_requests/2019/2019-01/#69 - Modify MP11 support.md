# #69  Modify MP11 support [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-19 20:18:42 UTC  
> Updated at: 2019-01-21 06:09:41 UTC  
> Merged at: 2019-01-20 12:36:30 UTC  
> Closed at: 2019-01-20 12:36:30 UTC  
> Url: https://github.com/boostorg/parameter/pull/69  

These are breaking changes to PR #66.  AFAICT this should not affect any dependent libraries.  No pull requests that have been accepted and merged onto those libraries rely on these features.  
  
* Remove are_tagged_arguments_mp11 and is_argument_pack_mp11.  They were reviewed as redundant.  
* Remove MP11 support for ArgumentPack models for now.  (This feature relied on templates that were not supposed to be specialized.)

---

## Comment 1

> Username: eldiener  
> Created_at: 2019-01-19 22:02:40 UTC  
> Url: https://github.com/boostorg/parameter/pull/69#issuecomment-455818943  

Tests are failing.

---
