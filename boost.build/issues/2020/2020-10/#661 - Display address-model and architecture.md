# #661 - [feature] Display address-model and architecture [Open]

> Username: sdarwin  
> Created at: 2020-10-01 22:19:00 UTC  
> Updated at: 2021-05-29 17:22:19 UTC  
> Url: https://github.com/boostorg/build/issues/661  

Feature request: display the address-model and architecture which are being used in the build.  
  
Problem description:  
  
Here is an example of running b2 with the address-model set to 32-bit.  
  
```  
../../b2 -a -j8 bench link=static variant=release address-model=32 toolset=clang  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...patience...  
...found 1616 targets...  
...updating 36 targets...  
```  
  
The user may wonder "did it catch that address-model flag? It appears to be building 64-bit, even though 32-bit was configured."  
  
Possible Solution 1:  
  
Display the latest accurate info without the word "default". Example:  
  
  
```  
../../b2 -a -j8 bench link=static variant=release address-model=32 toolset=clang  
Performing configuration checks  
  
    - address-model            : 32-bit  
    - architecture             : x86  
    - symlinks supported       : yes  
...patience...  
...found 1616 targets...  
...updating 36 targets...  
```  
  
Possible Solution 2:  
  
Show either the word "default" or "configured", depending on which one applies.    
  
Example 2a:  
  
```  
../../b2 -a -j8 bench link=static variant=release toolset=clang  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...patience...  
...found 1616 targets...  
...updating 36 targets...  
```  
  
Example 2b:  
  
```  
../../b2 -a -j8 bench link=static variant=release address-model=32 toolset=clang  
Performing configuration checks  
  
    - configured address-model : 32-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...patience...  
...found 1616 targets...  
...updating 36 targets...  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:49 UTC  
> Url: https://github.com/boostorg/build/issues/661#issuecomment-850868211  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
