# #597 Update instruction-set feature with new x86 targets [Merged]

> Username: Lastique  
> Created at: 2020-05-08 13:06:19 UTC  
> Updated at: 2021-10-02 21:02:01 UTC  
> Merged at: 2020-05-11 00:40:33 UTC  
> Closed at: 2020-05-11 00:40:33 UTC  
> Url: https://github.com/boostorg/build/pull/597  

* Changed icelake to icelake-client and icelake-server. The single icelake switch is not accepted by gcc.  
* Added cascadelake, cooperlake and tigerlake Intel targets.  
* Added znver2 AMD target.  
* Added c7 VIA target.  
* Reordered the list of targets in instruction-set definition to group targets by CPU vendors.  
* Updated toolsets with the new and updated values of instruction-set.  
* Updated .py files as well. Added instruction-set values that were missing compared to .jam files.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:00 UTC  
> Url: https://github.com/boostorg/build/pull/597#issuecomment-932820011  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
