# #18 - Travis configurations can be collapsed for efficiency [Closed]

> Username: pdimov  
> Created at: 2018-09-30 21:16:58 UTC  
> Updated at: 2018-11-01 18:01:04 UTC  
> Closed at: 2018-11-01 18:01:04 UTC  
> Url: https://github.com/boostorg/atomic/issues/18  

If you use `cxxstd=03` instead of `-std=c++03`, this would allow testing of more than one `cxxstd` level in one go, f.ex. `cxxstd=03,11,14,1z`.  
  
In addition, `address-model=32,64` is also possible.  
  
This avoids the common setup phase duplication and is therefore more efficient.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-01 18:01:04 UTC  
> Url: https://github.com/boostorg/atomic/issues/18#issuecomment-435129847  

Done.
