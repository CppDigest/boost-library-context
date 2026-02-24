# #651 style: Remove trailing space characters [Merged]

> Username: striezel  
> Created at: 2022-04-26 16:52:58 UTC  
> Updated at: 2022-04-26 19:19:36 UTC  
> Merged at: 2022-04-26 18:52:56 UTC  
> Closed at: 2022-04-26 18:52:56 UTC  
> Url: https://github.com/boostorg/gil/pull/651  

### Description  
  
Removes unnecessary trailing space characters in .cpp/.hpp files.  
  
No functional changes, this is just a "nice to have" cleanup thing.  
  
### References  
  
None.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-04-26 17:22:21 UTC  
> Url: https://github.com/boostorg/gil/pull/651#issuecomment-1110058510  

Azure Pipelines with Windows 2016 failed. To be more precise, it was cancelled on start. It shows the following error:  
  
> This is a scheduled windows-2016 brownout. The windows-2016 environment is deprecated and will be removed on April 1st, 2022. For more details, see https://github.com/actions/virtual-environments/issues/5238  
  
Looks like Microsoft is finally removing Windows 2016 / Visual Studio 2019 there, too, just like they did on the GitHub Actions environments a while ago. (Also see https://devblogs.microsoft.com/devops/hosted-pipelines-image-deprecation/.) So this means I will probably send another PR for the CI soon, this time updating or removing Windows 2016 stuff of Azure.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-26 18:52:24 UTC  
> Url: https://github.com/boostorg/gil/pull/651#issuecomment-1110139232  

> I will probably send another PR for the CI soon, this time updating or removing Windows 2016 stuff of Azure.  
  
👍  Thanks in advance!

---
