# #73 - Test standalone with libstdc++ [Closed]

> Username: vinniefalco  
> Created at: 2020-04-05 20:22:38 UTC  
> Updated at: 2020-08-12 22:01:13 UTC  
> Closed at: 2020-08-12 22:01:13 UTC  
> Url: https://github.com/boostorg/json/issues/73  

The Travis configuration for standalone clang-9 does not work because the installed libstdc++ is not new enough to contain <memory_resource>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-12 15:36:13 UTC  
> Url: https://github.com/boostorg/json/issues/73#issuecomment-672948581  

Maybe this is already fixed? Unsure

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-12 16:08:00 UTC  
> Updated at: 2020-08-12 16:08:15 UTC  
> Url: https://github.com/boostorg/json/issues/73#issuecomment-672967551  

I tested without any changes other than enabling the CI job and it still fails. I have a fix [here](https://github.com/sdkrystian/json/commit/566bd17bada75f7b3b1a00d89bff4526f3cb776e) but I'm unsure if this is the "right" solution.
