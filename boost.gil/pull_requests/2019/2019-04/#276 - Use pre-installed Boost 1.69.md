# #276 [azp] Use pre-installed Boost 1.69 [Closed]

> Username: mloskot  
> Created at: 2019-04-10 08:48:05 UTC  
> Updated at: 2019-04-13 12:31:23 UTC  
> Closed at: 2019-04-13 09:51:26 UTC  
> Url: https://github.com/boostorg/gil/pull/276  

Apparently, Azure Pipelines images of Ubuntu and Windows (VS2017, VS2019) now come with pre-installed Boost. Since this update, our AzP builds on Ubuntu started failing due to mismatch between Boost 1.68 we deploy and pre-installed Boost 1.69, see https://developercommunity.visualstudio.com/content/problem/525582/ubuntu-1604-image-changed-and-comes-with-usrlocals.html  
  
Let's try to avoid deploying of Boost 1.68 and use pre-installed Boost 1.69 where possible.  
  
### References:  
  
* C/C++: Add the latest 4 versions of Boost 1.66.0 - 1.69.0 (Linux, Windows)  
  https://github.com/Microsoft/azure-pipelines-image-generation/pull/732  
  
* Document available versions of Boost 1.66.0 - 1.69.0 following #732 update  
  https://github.com/Microsoft/azure-pipelines-image-generation/issues/845  
  
### Tasklist  
  
- [ ] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-04-10 19:38:09 UTC  
> Url: https://github.com/boostorg/gil/pull/276#issuecomment-481833011  

### Status  
  
Current status is https://dev.azure.com/boostorg/gil/_build/results?buildId=327  
  
* Builds on Ubuntu+GCC with pre-installed Boost 1.69 pass.  
* Builds on macOS+clang and Windows+VS2015 with manually deployed Boost 1.68 pass.  
* Builds on Windows+VS2017 with pre-installed Boost 1.69 fail due to run-time failures of tests  
  
I am suspicious about the pre-installed Boost 1.69: it comes with `include/boost/gil` headers which may be mixed and matched with headers cloned from the Boost.GIL repo. This may lead to subtle issues causing the `legacy/image` checksum tests failing.  
  
### Plan  
  
My plan is to leave this PR for now and try to re-install Boost 1.68 on all AzP builds and force to use it without detecting the pre-installed Boost 1.69.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-04-10 21:16:14 UTC  
> Url: https://github.com/boostorg/gil/pull/276#issuecomment-481868489  

For now, it is reasonable to stick to Boost 1.68 installed my our AzP scripts as all builds are successful (see #277)

---
