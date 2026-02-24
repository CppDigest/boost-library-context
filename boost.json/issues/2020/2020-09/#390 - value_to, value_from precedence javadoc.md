# #390 - value_to, value_from precedence javadoc [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:11:50 UTC  
> Updated at: 2023-03-02 13:00:15 UTC  
> Closed at: 2023-03-02 13:00:14 UTC  
> Url: https://github.com/boostorg/json/issues/390  

value_to is unclear about the precedence of the three listed options (constructor, library provided conversion or tag_invoke) if multiple are present provided this is actually possible. The same is true for value_from.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-25 17:10:33 UTC  
> Url: https://github.com/boostorg/json/issues/390#issuecomment-699047093  

https://develop.json.cpp.al/json/usage/conversion.html  
  
> The library provided overloads have no special prioritization over those provided by the user, so care should be taken to avoid writing ambiguous declarations:

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-25 17:25:16 UTC  
> Url: https://github.com/boostorg/json/issues/390#issuecomment-699054201  

We are removing the constructor, and the javadocs should probably repeat the warning

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-09-30 18:02:15 UTC  
> Url: https://github.com/boostorg/json/issues/390#issuecomment-701551084  

Sounds good

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-03-02 13:00:14 UTC  
> Url: https://github.com/boostorg/json/issues/390#issuecomment-1451830558  

The order is now documented (as of release 1.81).
