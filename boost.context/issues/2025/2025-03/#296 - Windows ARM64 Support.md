# #296 - Windows ARM64 Support [Open]

> Username: mborland  
> Created at: 2025-03-07 17:00:40 UTC  
> Updated at: 2025-08-28 16:05:58 UTC  
> Url: https://github.com/boostorg/context/issues/296  

Two probably related issues with using Windows on ARM64 using Parallels:  
  
1) When using B2 the output of configuration checks is:  
  
```  
    - default address-model    : 64-bit [1]  
    - default architecture     : none [1]  
```  
  
If you specify `architecture=arm` on the command line things build and run just fine  
  
2) When using CMAKE the configuration step successfully completes, but the build always fails with: `unknown command-line argument or argument value -DBOOST_CONTEXT_EXPORT=`  
  
I am able to build and run CMAKE using the x64 cross compiler after manually specifying the architecture and the assembler in Visual Studio.

---

## Comment 1

> Username: Greenie0701  
> Created at: 2025-08-28 16:05:58 UTC  
> Url: https://github.com/boostorg/context/issues/296#issuecomment-3234098728  

Probably if anyone wondering, how to build context on windows arm64, you can add the following configs with your cmake to get a successful build on windows arm64:  
  
`-DBOOST_CONTEXT_IMPLEMENTATION=winfib -DBOOST_EXCLUDE_LIBRARIES='mpi;graph_parallel;coroutine'`
