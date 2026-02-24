# #156 Add the C++11 requirements also to the test project [Merged]

> Username: Flamefire  
> Created at: 2022-03-09 17:33:20 UTC  
> Updated at: 2022-03-09 18:09:38 UTC  
> Merged at: 2022-03-09 18:08:50 UTC  
> Closed at: 2022-03-09 18:08:50 UTC  
> Url: https://github.com/boostorg/wave/pull/156  

Followup to https://github.com/boostorg/wave/pull/154  
  
I hope I got this right, doesn't Appveyor run for PRs too?  
I was also a bit confused about the "build" subfolder of test which I've seen here for the first time. Wouldn't it be easier to put that into `test/Jamfile` and get rid of the `test/build` folder?  
  
Anyway, check if this makes sense and passes on the updated Appveyor config or if there need to be similar checks elsewhere

---
