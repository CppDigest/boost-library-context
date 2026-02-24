# #429 - Broken URL in build/Jamfile [Open]

> Username: jwakely  
> Created at: 2024-01-19 15:28:17 UTC  
> Updated at: 2024-01-19 15:32:41 UTC  
> Url: https://github.com/boostorg/python/issues/429  

https://github.com/boostorg/python/blob/6c3f3ecacf66f61d799d80294bbf59ceb84daf8a/build/Jamfile#L27C31-L27C81  
  
The http://www.boost.org/libs/python/doc/building.html URL gives a 404 (and should probably be https).

---

## Comment 1

> Username: jwakely  
> Created at: 2024-01-19 15:30:30 UTC  
> Url: https://github.com/boostorg/python/issues/429#issuecomment-1900632627  

Looks like it should be doc/html/building.html instead of doc/building.html

---

## Comment 2

> Username: jwakely  
> Created at: 2024-01-19 15:32:40 UTC  
> Url: https://github.com/boostorg/python/issues/429#issuecomment-1900635859  

https://www.boost.org/doc/libs/1_84_0/libs/python/doc/html/building/include_issues.html has another broken non-https URL.
