# #49 Features/bug fixes [Closed]

> Username: frenchtoast747  
> Created at: 2014-11-05 21:13:47 UTC  
> Updated at: 2021-10-02 22:20:40 UTC  
> Closed at: 2014-11-06 06:51:40 UTC  
> Url: https://github.com/boostorg/build/pull/49  

#### feature.py  
  
`values` is a function in the `feature` module. Simply printing `values` just printed the `__str__` of the function.  
#### property.py  
  
Sorting by `Feature` instance rather than the feature's name produces unpredictable results since sorting instances of classes that don't have comparison methods implemented end up sorting by the instance's id() which, in CPython, is usually the instance's memory address.  
  
This would cause system tests to fail randomly. For example, consider the configuration `<toolset>adi <toolset-adi:family>bf <toolset-adi:version>7.2`. The paths created for that particular toolset would alternate randomly between `adi-bf-7.2` and `adi-7.2-bf`. So, checking for an `expected_addition()` in the system test would fail randomly.  
#### build_system.py  
  
`PARALLELISM` was converted to a string.  
  
Support for `--version` was added.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-11-06 06:51:40 UTC  
> Url: https://github.com/boostorg/build/pull/49#issuecomment-61934293  

Thanks, all fixes are merged now.

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2014-11-06 16:57:39 UTC  
> Url: https://github.com/boostorg/build/pull/49#issuecomment-62012152  

Thanks for the merge!

---
