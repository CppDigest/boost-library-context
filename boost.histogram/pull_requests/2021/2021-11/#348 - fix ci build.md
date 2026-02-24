# #348 fix ci build [Merged]

> Username: HDembinski  
> Created at: 2021-11-07 11:44:56 UTC  
> Updated at: 2021-11-07 12:30:06 UTC  
> Merged at: 2021-11-07 12:30:05 UTC  
> Closed at: 2021-11-07 12:30:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/348  

Closes #347   
  
#### Changes to cmake builds  
- Superproject build cannot run tests anymore, so now it is a simple build test for the superproject  
- Tests need to be compiled with `make check` to work or rather `cmake --build . --target check`, `-k` option does not work on windows so it was removed

---
