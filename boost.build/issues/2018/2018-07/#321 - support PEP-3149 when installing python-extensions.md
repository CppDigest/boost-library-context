# #321 - support PEP-3149 when installing python-extensions [Open]

> Username: xnox  
> Created at: 2018-07-04 12:04:19 UTC  
> Updated at: 2021-06-26 03:10:06 UTC  
> Url: https://github.com/boostorg/build/issues/321  

Python extensions, like `mpi.so` should not be staged as `mpi.so` but instead should be staged as e.g. `mpi.cpython-32mu.so` where the suffix or tag can be figured out with  
  
```  
>>> sysconfig.get_config_var('EXT_SUFFIX')  
'.cpython-32mu.so'  
>>> sysconfig.get_config_var('SOABI')  
'cpython-32mu'  
```  
  
Such that PEP-3149 becomes supported, and one can trivially reuse the same stagedir for multiple python builds.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:25 UTC  
> Url: https://github.com/boostorg/build/issues/321#issuecomment-868936437  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
