# #38 Use GNUInstallDirs instead of hard wiring install directories [Closed]

> Username: antonblanchard  
> Created at: 2021-08-16 23:56:00 UTC  
> Updated at: 2021-09-30 15:04:48 UTC  
> Closed at: 2021-09-30 15:04:48 UTC  
> Url: https://github.com/boostorg/url/pull/38  

On a multiarch setup cmake files should go into lib64.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-08-17 19:44:46 UTC  
> Url: https://github.com/boostorg/url/pull/38#issuecomment-900581471  

Well, as this is intended to be a Boost library, it will use the conventions of the Boost project. Also note that this project is on pause for now while I work on my other libraries.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-09-30 15:04:48 UTC  
> Url: https://github.com/boostorg/url/pull/38#issuecomment-931406555  

The CML should work now for all supported use-cases

---
