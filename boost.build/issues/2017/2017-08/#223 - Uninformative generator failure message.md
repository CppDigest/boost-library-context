# #223 - Uninformative generator failure message [Open]

> Username: grafikrobot  
> Created at: 2017-08-01 16:16:46 UTC  
> Updated at: 2017-09-15 20:15:59 UTC  
> Url: https://github.com/boostorg/build/issues/223  

When targets fail to build because it can't find a viable generator transformation we give a rather terse warning about it:  
  
`warn: Unable to construct some/project/target`  
  
[This message](https://github.com/boostorg/build/blob/develop/src/build/targets.jam#L1556) needs provide way more information as to what is going on so that users can resolve the problem.

---

## Comment 1

> Username: chambm  
> Created at: 2017-09-15 20:15:59 UTC  
> Url: https://github.com/boostorg/build/issues/223#issuecomment-329890804  

Yeah I'm hitting my head against this now. Trying to add a .NET assembly derivation of SHARED_LIB with appropriate semantics in msvc.jam. But I get this from `--debug-generators`:  
```  
find-viable-generators target-type= NET_ASSEMBLY property-set= msvc-12.0/rls/adrs-mdl-64/thrd-mlt/us-clr-tr  
trying type NET_ASSEMBLY  
there are generators for this type  
trying generator builtin.assembly-generator ( CPP -> NET_ASSEMBLY )  
  is viable  
     *** construct NET_ASSEMBLY  
         from { msvc%msvc.compile.c++clr-ParamTypes.obj.OBJ { common/ParamTypes.cpp.CPP } }  
         from { msvc%msvc.compile.c++clr-IterationListener.obj.OBJ { common/IterationListener.cpp.CPP } }  
         ...snip...  
         properties: <address-model>64 ...snip...  
     ***  1  viable generators  
       ** generator 'builtin.assembly-generator' pruned  
warn: Unable to construct blah blah dll  
```  
With no explanation of why the only viable generator was pruned.
