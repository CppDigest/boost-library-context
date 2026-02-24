# #287 - Pointer overload for CharSet parameters [Closed]

> Username: vinniefalco  
> Created at: 2022-08-01 19:46:21 UTC  
> Updated at: 2022-08-03 20:22:56 UTC  
> Closed at: 2022-08-03 20:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/287  

Rule factory functions that accept CharSets as parameters could have pointer overloads, to emit rules that require less storage space when the character set is a compile-time constant.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-02 02:06:20 UTC  
> Url: https://github.com/boostorg/url/issues/287#issuecomment-1201932074  

Partially done

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 20:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/287#issuecomment-1204436306  

This is replaced by `ref` which is better in every way
