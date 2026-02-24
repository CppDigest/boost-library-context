# #76 - how do I build the docs? [Closed]

> Username: vinniefalco  
> Created at: 2023-03-01 05:04:00 UTC  
> Updated at: 2023-03-03 07:08:23 UTC  
> Closed at: 2023-03-03 07:08:23 UTC  
> Url: https://github.com/boostorg/redis/issues/76  

I don't see instructions

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-03-01 07:28:17 UTC  
> Url: https://github.com/boostorg/redis/issues/76#issuecomment-1449480980  

Where do you expect the instructions? I am using cmake built-in Doxygen support with a preset, for example  
  
```  
cmake --preset dev  
cmake --build --preset dev --target doc  
```  
  
Will generate the docs in   
  
`"DOXYGEN_OUTPUT_DIRECTORY": "${sourceDir}/build/dev/doc/"`  
  
See CMakePreset.json.
