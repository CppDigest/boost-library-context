# #189 ensure related functions are scoped correctly [Merged]

> Username: grisumbras  
> Created at: 2024-11-03 12:47:28 UTC  
> Updated at: 2024-11-03 13:51:07 UTC  
> Merged at: 2024-11-03 13:28:27 UTC  
> Closed at: 2024-11-03 13:28:27 UTC  
> Url: https://github.com/boostorg/docca/pull/189  

The assumption is that related functions are "members" of classes they relate to. This assumption could be violated with recent Doxygen if the first compound to have them as members is their namespace. Now Docca makes sure to remove such functions from every scope but one, and also makes sure that last scope is a class.

---
