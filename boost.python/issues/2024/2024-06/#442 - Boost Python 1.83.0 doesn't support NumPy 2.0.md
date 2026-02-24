# #442 - Boost Python 1.83.0 doesn't support NumPy 2.0 [Open]

> Username: neel  
> Created at: 2024-06-18 00:04:02 UTC  
> Updated at: 2024-10-24 14:45:28 UTC  
> Url: https://github.com/boostorg/python/issues/442  

I am getting this error while using Boost.Python  
```  
A module that was compiled using NumPy 1.x cannot be run in  
NumPy 2.0.0 as it may crash. To support both 1.x and 2.x  
versions of NumPy, modules must be compiled with NumPy 2.0.  
Some module may need to rebuild instead e.g. with 'pybind11>=2.12'.  
  
If you are a user of the module, the easiest solution will be to  
downgrade to 'numpy<2' or try to upgrade the affected module.  
We expect that some modules will need time to support NumPy 2.  
```  
  
I'vent attached a reproducible example because it is a known issue in PyBind https://github.com/pybind/pybind11/issues/5009   
  
Is this issue resolved in Boost Python ? Do there exist any workaround ?

---

## Comment 1

> Username: bernhardkaindl  
> Created at: 2024-10-24 14:45:27 UTC  
> Url: https://github.com/boostorg/python/issues/442#issuecomment-2435503662  

@neel wrote:  
> Is this issue resolved in Boost Python ? Do there exist any workaround ?  
  
There appears to be a patch that should fix the `elsize` issue. I found it this way:  
  
- https://www.google.com/search?client=firefox-b-d&q=boost+numpy+version+2  
  - https://github.com/rdkit/rdkit/issues/7477  
    - https://github.com/boostorg/python/pull/432  
      - https://github.com/boostorg/python/pull/432/files - Gotcha!
