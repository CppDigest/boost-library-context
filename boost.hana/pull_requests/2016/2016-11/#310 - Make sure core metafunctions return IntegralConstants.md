# #310 Make sure core metafunctions return IntegralConstants [Open]

> Username: ldionne  
> Created at: 2016-11-17 08:13:18 UTC  
> Updated at: 2021-03-31 11:03:36 UTC  
> Url: https://github.com/boostorg/hana/pull/310  

This PR is an attempt to address #54, and I will be closing #54 in favor of this PR.  
  
This does not work right now, because we would need to include the full definition of `hana::integral_constant` in core metafunctions, and that introduces circular dependencies.

---
