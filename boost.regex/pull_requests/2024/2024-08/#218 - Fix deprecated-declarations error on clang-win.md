# #218 Fix deprecated-declarations error on clang-win [Merged]

> Username: k3DW  
> Created at: 2024-08-23 04:31:13 UTC  
> Updated at: 2024-08-24 15:05:29 UTC  
> Merged at: 2024-08-24 10:35:32 UTC  
> Closed at: 2024-08-24 10:35:32 UTC  
> Url: https://github.com/boostorg/regex/pull/218  

This error was seen on the CI of a Boost.Unordered PR, boostorg/unordered#274. Note the failing job [here](https://github.com/boostorg/unordered/actions/runs/10517392958/job/29141588762).  
  
This fix is inspired by [this commit](https://github.com/boostorg/interprocess/commit/2449ba1c743e05f45c991ac27de7bf4621aa2118).

---

## Comment 1

> Username: k3DW  
> Created_at: 2024-08-23 19:49:34 UTC  
> Url: https://github.com/boostorg/regex/pull/218#issuecomment-2307718351  

I see 1 of the CI jobs failing. This job is also failing in an identical way on the `develop` branch, so it's unrelated to my change here.

---
