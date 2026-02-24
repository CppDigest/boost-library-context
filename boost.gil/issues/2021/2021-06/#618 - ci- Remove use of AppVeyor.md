# #618 - ci: Remove use of AppVeyor [Open]

> Username: mloskot  
> Created at: 2021-06-17 23:06:28 UTC  
> Updated at: 2022-04-26 17:30:35 UTC  
> Url: https://github.com/boostorg/gil/issues/618  

### Motivation  
  
Following @sdebionne question in https://github.com/boostorg/gil/pull/596#issuecomment-851273219 I think it's time to get rid of AppVeyor and get closer to the overall Boost trend to switch to GitHub Actions. We're still having some support from Azure Pipelines, however it's different (or not) from the GA, who knows.  
  
### State  
  
Our GA+AZ:  
  
https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/.github/workflows/ci.yml#L149-L160  
https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/.azure-pipelines.yml#L56  
https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/.azure-pipelines.yml#L73  
  
is covering most of our AV:  
https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/.appveyor.yml#L20-L63  
  
### Future  
  
The only difference being release and debug build variants tested on AV, but that should be easy to add to GA.

---

## Comment 1

> Username: striezel  
> Created at: 2022-04-26 17:30:35 UTC  
> Url: https://github.com/boostorg/gil/issues/618#issuecomment-1110066232  

> We're still having some support from Azure Pipelines, however it's different (or not) from the GA, who knows.  
  
Azure Pipelines is in the process of removing Windows Server 2016 with Visual Studio 2017, so it will no longer be available. See https://devblogs.microsoft.com/devops/hosted-pipelines-image-deprecation/ for more information.  
  
However, Azure seems indeed to be using the same stuff as GitHub Actions, so we are still covered when it comes to MSVC.
