# #643 Switch Azure's macOS build to supported OS version [Merged]

> Username: striezel  
> Created at: 2022-04-07 16:52:09 UTC  
> Updated at: 2022-04-07 18:11:58 UTC  
> Merged at: 2022-04-07 18:03:40 UTC  
> Closed at: 2022-04-07 18:03:40 UTC  
> Url: https://github.com/boostorg/gil/pull/643  

### Description  
  
The image `macOS-10.14` is not available anymore, but `macOS-10.15` is still available. So this PR simply switches to that image.  
  
If this is successful, then at least all the Azure pipeline builds of GIL are passing. :)  
  
### References  
  
See <https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml> for available images in Azure pipelines.  
  
This builds on the following idea mentioned in #642:  
  
> As a general idea, we should probably make sure that the existing CI pipelines pass (except for the outdated stuff that may potentially get removed). Otherwise we may just be migrating existing build failures to another CI configuration with boost-ci.  
  
### Tasklist  
  
- [x] Ensure Azure pipeline CI with macOS build passes  
- [ ] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-04-07 17:44:54 UTC  
> Url: https://github.com/boostorg/gil/pull/643#issuecomment-1092023048  

Yay, all jobs on Azure Pipelines are now successful: https://dev.azure.com/boostorg/gil/_build/results?buildId=1790&view=results :-)

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-07 18:03:32 UTC  
> Url: https://github.com/boostorg/gil/pull/643#issuecomment-1092048781  

Awesome!

---
