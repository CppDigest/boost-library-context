# #652 ci: remove Windows Server 2016 from builds on Azure Pipelines [Merged]

> Username: striezel  
> Created at: 2022-04-26 17:35:44 UTC  
> Updated at: 2022-06-18 16:04:12 UTC  
> Merged at: 2022-04-26 19:07:32 UTC  
> Closed at: 2022-04-26 19:07:32 UTC  
> Url: https://github.com/boostorg/gil/pull/652  

### Description  
  
The images with Windows Server 2016 / Visual Studio 2017 have  
been deprecated by Microsoft and are in the process of being  
removed completely, so that no further builds with that image  
will be possible.  
  
I've just encountered the problem in https://github.com/boostorg/gil/pull/651  
where the VS 2017 / Windows 2016 builds refused to start:  
  
https://dev.azure.com/boostorg/gil/_build/results?buildId=1813&view=results  
  
### References  
  
For reference, see  
* <https://devblogs.microsoft.com/devops/hosted-pipelines-image-deprecation/>  
* <https://github.com/actions/virtual-environments/issues/5238>  
* <https://github.com/actions/virtual-environments/issues/5403>  
  
Since it seems that Microsoft is basically using the same build  
environments on both Azure and GitHub Actions when it comes to  
Windows and Visual Studio, I decided to remove those jobs instead  
of updating them to a newer version of the image. The tests that  
would be possible with newer images on Azure are basically what  
we are already doing with GitHub Actions here.  
  
Looks like the new final deadline for removal of Windows 2016 is  
30th June 2022. But until that happens there will be several     
"brownouts" lasting 24 hours each where users will see error   
messages that indicate the image will be removed soon. This  
basically means we cannot rely on Windows 2016 anymore, even  
before 30th June.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: gene-keys  
> Created_at: 2022-04-26 18:27:26 UTC  
> Url: https://github.com/boostorg/gil/pull/652#issuecomment-1110118378  

IMPORTANT NOTE FOR THOSE OF YOU STILL STRUGGLING WITH WIN2016 BROWNOUT NOTICE.  
  
CHECK EVERYTHING!  
  
NOT JUST YOUR PIPELINE CODE, CHECK PIPELINE TRIGGERS POOLS. CHECK RELEASES POOLS, CHECK ALL THE POOLS EVERYWHERE ON YOUR DEVOPS ACCOUNT. WHAT WILL HAPPEN IS YOU WILL EVENTUALLY FIND SOMETHING STILL SET TO USE WIN2016 POOL. ONCE YOU FIND ALL OF THEM YOUR ACCOUNT WILL NO LONGER GET THE BROWNOUT NOTICE EVEN DURING THE SCHEDULED TIMES. SO IF YOU ARE GETTING THE NOTICE ITS NOT YOUR WIN2017 POOL, OR WIN-LATEST IN THE YML, ITS SOMETHING ELSE,  
  
AZURE NOTICES ARE DOING A TERRIBLE JOB ON THIS WITH THEIR WORDING AND WHERE THE NOTICE APPEARS. THE NOTICE MEANS YOUR AZURE DEVOPS ACCOUNT IS STILL USING WINDWOS POOL SOMEWHERE, YOU DID NOT CHECK EVEYWHERE.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-26 19:06:05 UTC  
> Url: https://github.com/boostorg/gil/pull/652#issuecomment-1110150537  

AFAICT, nothing uses Win 2016 pools anywhere under `https://dev.azure.com/boostorg` where GIL's CI happen on AzP.   
So, I think we're good.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2022-04-26 19:07:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/652#pullrequestreview-953875176  

LGTM, thanks!

---
