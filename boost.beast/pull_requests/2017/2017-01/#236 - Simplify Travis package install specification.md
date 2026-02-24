# #236 Simplify Travis package install specification [Closed]

> Username: ximinez  
> Created at: 2017-01-23 23:44:10 UTC  
> Updated at: 2017-01-24 18:57:17 UTC  
> Closed at: 2017-01-24 18:57:17 UTC  
> Url: https://github.com/boostorg/beast/pull/236  

Because all build matrix entries have the same package dependencies, this moves that info to the global config. Allows the rest of the matrix to be edited with impunity.

---

## Comment 1

> Username: codecov-io  
> Created_at: 2017-01-24 00:06:47 UTC  
> Url: https://github.com/boostorg/beast/pull/236#issuecomment-274659081  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/236?src=pr) is 96.07% (diff: 100%)  
> Merging [#236](https://codecov.io/gh/vinniefalco/Beast/pull/236?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
```diff  
@@             master       #236   diff @@  
==========================================  
  Files            86         86            
  Lines          5780       5780            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           5553       5553            
  Misses          227        227            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [c00cd37...28c00ca](https://codecov.io/gh/vinniefalco/Beast/compare/c00cd37b8a441a92755658014fdde97d515ec7ed...28c00ca8daab5704f7b07fb753c48cd0ea9506c9?src=pr)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-01-24 00:18:18 UTC  
> Url: https://github.com/boostorg/beast/pull/236#issuecomment-274661270  

Lipstick on a pig :)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-01-24 18:57:17 UTC  
> Url: https://github.com/boostorg/beast/pull/236#issuecomment-274900476  

Part of #233, thanks

---
