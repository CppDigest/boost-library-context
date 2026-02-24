# #387 - array ctor javadoc [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:07:53 UTC  
> Updated at: 2020-10-18 15:51:40 UTC  
> Closed at: 2020-10-18 15:51:40 UTC  
> Url: https://github.com/boostorg/json/issues/387  

The initializer_list constructor for array is listed under move constructors.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-24 17:00:33 UTC  
> Url: https://github.com/boostorg/json/issues/387#issuecomment-698468273  

This looks like a docca problem because the brief says "Constructor." But it is listed under "Move constructors."

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-26 01:40:03 UTC  
> Url: https://github.com/boostorg/json/issues/387#issuecomment-699266799  

![image](https://user-images.githubusercontent.com/1503976/94327256-85802d00-ff5e-11ea-86a8-87302944629a.png)

---

## Comment 3

> Username: evanlenz  
> Created at: 2020-10-17 04:13:05 UTC  
> Url: https://github.com/boostorg/json/issues/387#issuecomment-710744226  

This should be fixed now as a result of merging boostorg/docca#42.

---

## Comment 4

> Username: evanlenz  
> Created at: 2020-10-18 07:25:22 UTC  
> Updated at: 2020-10-18 07:26:14 UTC  
> Url: https://github.com/boostorg/json/issues/387#issuecomment-711128571  

This should be fixed once we close/rebase https://github.com/boostorg/docca/pull/46

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-10-18 15:51:40 UTC  
> Url: https://github.com/boostorg/json/issues/387#issuecomment-711245078  

Yep, fixed
