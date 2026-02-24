# #469 - coverage badge shows the last pull request instead of the develop branch [Open]

> Username: vinniefalco  
> Created at: 2022-08-29 02:44:05 UTC  
> Updated at: 2022-09-27 21:43:37 UTC  
> Url: https://github.com/boostorg/url/issues/469  

![image](https://user-images.githubusercontent.com/1503976/187112655-0fb6bee9-9618-4d7b-98e1-d2be0a699d02.png)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-31 00:33:10 UTC  
> Url: https://github.com/boostorg/url/issues/469#issuecomment-1232312138  

Mmmm... that's what I get from https://app.codecov.io/gh/CPPAlliance/url/settings/badge:  
  
```markdown  
[![codecov](https://codecov.io/gh/CPPAlliance/url/branch/develop/graph/badge.svg?token=jUkdNr0eJi)](https://codecov.io/gh/CPPAlliance/url)  
```  
  
[![codecov](https://codecov.io/gh/CPPAlliance/url/branch/develop/graph/badge.svg?token=jUkdNr0eJi)](https://codecov.io/gh/CPPAlliance/url)  
  
So there's nothing we can do here regarding the link.   
  
Unfortunately, codecov associates whatever is in your fork's `develop` branch with CppAlliance's `develop`:  
  
https://app.codecov.io/gh/CPPAlliance/url/commits  
  
That never happens with any of my branches.   
  
Maybe that's just the branch name? Where could that association be coming from?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-09-27 18:12:05 UTC  
> Url: https://github.com/boostorg/url/issues/469#issuecomment-1259876842  

Are we moving from codecov with our own reports?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-27 21:43:37 UTC  
> Url: https://github.com/boostorg/url/issues/469#issuecomment-1260086089  

> Are we moving from codecov with our own reports?  
  
Not yet, because our own reports look terrible
