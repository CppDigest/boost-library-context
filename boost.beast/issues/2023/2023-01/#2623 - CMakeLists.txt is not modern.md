# #2623 - CMakeLists.txt is not modern [Closed]

> Username: vinniefalco  
> Created at: 2023-01-15 17:33:28 UTC  
> Updated at: 2025-04-27 16:29:11 UTC  
> Closed at: 2025-04-27 16:29:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2623  

The Beast CML is old and out of date. It doesn't pull in the vcxproj for dependencies the way that JSON and URL do. And it copies the header virtual folders into every project, which it should not do. There should be a specific library target with the include folder.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-04-27 16:29:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2623#issuecomment-2833536493  

Addressed in #2954.
