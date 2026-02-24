# #78 use -undefined dynamic_lookup on darwin in python_for_extensions [Closed]

> Username: tdsmith  
> Created at: 2015-05-05 05:22:18 UTC  
> Updated at: 2021-10-02 22:20:28 UTC  
> Closed at: 2015-05-05 06:53:35 UTC  
> Url: https://github.com/boostorg/build/pull/78  

Passing -undefined dynamic_lookup to the linker (instead of -lpython or  
-framework Python) permits undefined symbols in shared libraries on OS  
X. This allows a module to be linked against one Python framework and  
imported from another.  
  
Tested by building ledger, which uses Boost.Python, against a version of Boost built with and without this patch. In the "before" gist, libboost_python-mt.dylib has a linkage to `/usr/local/Frameworks/Python.framework/Versions/2.7/Python`; in the "after" gist, it does not, and the ledger python demo still runs correctly. https://gist.github.com/tdsmith/558243ad98ad288a16e0  
  
Closes #69.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-05-05 06:53:35 UTC  
> Url: https://github.com/boostorg/build/pull/78#issuecomment-98973141  

Thanks, cherry-picked.

---
