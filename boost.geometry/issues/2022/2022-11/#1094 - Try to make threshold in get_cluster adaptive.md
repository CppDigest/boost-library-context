# #1094 - Try to make threshold in get_cluster adaptive [Open]

> Username: barendgehrels  
> Created at: 2022-11-16 15:34:18 UTC  
> Updated at: 2022-11-24 17:31:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1094  

See issue #1081 and PR #1093

---

## Comment 1

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-11-24 17:31:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1094#issuecomment-1326721072  

I have 83 cases that the difference of threshold (1 or 3) makes different result.  
In some cases, 3 is better, the others, 1 is better.  
These are the test codes.  
[get_culusters_1vs3.zip](https://github.com/boostorg/geometry/files/10086552/get_culusters_1vs3.zip)  
  
I have tested them on Visual Studio 2019, debug, x64.  
To avoid stack overflow, we need to set /STACK on MSVC linker options.  
https://learn.microsoft.com/en-us/cpp/build/reference/stack-stack-allocations?view=msvc-170
