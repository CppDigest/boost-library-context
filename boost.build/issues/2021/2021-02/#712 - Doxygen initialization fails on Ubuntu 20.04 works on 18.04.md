# #712 - Doxygen initialization fails on Ubuntu 20.04 works on 18.04 [Open]

> Username: mloskot  
> Created at: 2021-02-19 20:48:21 UTC  
> Updated at: 2021-05-29 16:22:55 UTC  
> Url: https://github.com/boostorg/build/issues/712  

In my [doc/Jamfile](https://github.com/boostorg/gil/blob/1e8526797e3327fd1bed7c2ace092fad5fd5ee74/doc/Jamfile#L12-L13), I have this  
  
```  
.doxygen = [ doxygen.name ] ;  
.doxygen ?= doxygen ;  
```  
  
On Ubuntu 20.04, I'm observing the Doxygen initialisation failure:  
  
![image](https://user-images.githubusercontent.com/80741/108559500-b1c88b80-72fb-11eb-8a02-8023ef52c64a.png)  
  
On Ubuntu 18.04, `b2` succeeds to build the dox  
  
![image](https://user-images.githubusercontent.com/80741/108559561-bf7e1100-72fb-11eb-8c96-8897c9084dbc.png)  
  
Both environments,  
- WSL 2  
- using the same version of Doxygen and Boost.Build  
- using Boost superproject reset to `develop` branch with latest `b2` bootstrapped  
- building `libs/gil/doc` having the `libs/gil` submodule also at `develop` branch  
  
This is a peculiar issue but I have no idea what is going on, so documenting it here.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:25 UTC  
> Url: https://github.com/boostorg/build/issues/712#issuecomment-850859598  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
