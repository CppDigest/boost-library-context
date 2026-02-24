# #475 - intel-linux toolset should pass -std=c++11 to compiler [Open]

> Username: kostrzewa  
> Created at: 2019-08-28 11:38:17 UTC  
> Updated at: 2021-06-11 01:55:24 UTC  
> Url: https://github.com/boostorg/build/issues/475  

For a while now, as far as I know, ICC on Linux has had most command line arguments adjusted to be more or less gcc-compatible. As a result, the only way I was able to make Boost submodules compile using Intel 18.0.x on Linux (albeit on a Cray) was to modify ```tools/build/src/engine/build.sh``` to feature:  
  
```  
B2_CXX="${CXX} -std=c++11"  
```  
  
in the corresponding switch statement. This was with the 1_71_0 release tarball.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:44 UTC  
> Url: https://github.com/boostorg/build/issues/475#issuecomment-859203200  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
