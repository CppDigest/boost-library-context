# #185 Fix/duplicate entities [Merged]

> Username: grisumbras  
> Created at: 2024-10-31 10:24:08 UTC  
> Updated at: 2024-11-03 13:27:42 UTC  
> Merged at: 2024-10-31 11:25:50 UTC  
> Closed at: 2024-10-31 11:25:50 UTC  
> Url: https://github.com/boostorg/docca/pull/185  

Fix #170.  
Fix #159.  
Fix #184.  
  
This is really a bandaid. The treatment of functions should change to properly accomodate multiple references to them from different scopes.

---

## Comment 1

> Username: grisumbras  
> Created_at: 2024-10-31 10:25:00 UTC  
> Url: https://github.com/boostorg/docca/pull/185#issuecomment-2449519118  

@anarthal please check that the resulting docs are what they are supposed to be.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-10-31 10:35:12 UTC  
> Url: https://github.com/boostorg/docca/pull/185#issuecomment-2449536982  

An automated preview of the documentation is available at [https://185.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://185.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 3

> Username: anarthal  
> Created_at: 2024-11-02 16:40:54 UTC  
> Url: https://github.com/boostorg/docca/pull/185#issuecomment-2453043511  

Jobs no longer fail, but the generated pages contain invalid links.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2024-11-03 11:09:42 UTC  
> Url: https://github.com/boostorg/docca/pull/185#issuecomment-2453387138  

Huh, I didn't think about that. Indeed, a function will be scoped randomly to either the class or the namespace. And if it is scoped to the namespace the link would be totally different. For now I'll make another "bandaid" that puts the entity into the class. But in the future I will have to significantly change the way I deal with functions.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2024-11-03 13:27:41 UTC  
> Url: https://github.com/boostorg/docca/pull/185#issuecomment-2453428140  

#189

---
