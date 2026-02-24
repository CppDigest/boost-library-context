# #853 Add support for modular build structure. [Closed]

> Username: grafikrobot  
> Created at: 2024-01-23 14:37:01 UTC  
> Updated at: 2024-01-23 15:52:39 UTC  
> Closed at: 2024-01-23 15:52:39 UTC  
> Url: https://github.com/boostorg/boost/pull/853  

This adds the minimal set of declarations to make it possible to have both legacy non-modular building work with new modular building. It allows incremental addition in libraries to get them ready for modular building.  
  
This is a temporary state though. After all libraries, tools, and testing build in this mixed mode another version of the jamroot will be needed to remove the legacy build handling.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-01-23 15:50:56 UTC  
> Url: https://github.com/boostorg/boost/pull/853#issuecomment-1906358948  

This PR should target `develop`.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-01-23 15:52:39 UTC  
> Url: https://github.com/boostorg/boost/pull/853#issuecomment-1906362382  

> This PR should target `develop`.  
  
Do'h :-(

---
