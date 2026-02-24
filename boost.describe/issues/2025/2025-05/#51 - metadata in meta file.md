# #51 - metadata in meta file [Open]

> Username: sdarwin  
> Created at: 2025-05-13 10:40:36 UTC  
> Updated at: 2025-05-13 12:58:27 UTC  
> Url: https://github.com/boostorg/describe/issues/51  

"A C++14 reflection library"  
  
C++14 should be mentioned in `/meta/libraries.json`

---

## Comment 1

> Username: pdimov  
> Created at: 2025-05-13 11:40:25 UTC  
> Url: https://github.com/boostorg/describe/issues/51#issuecomment-2876162571  

Describe is a bit special in that while the functionality is C++14, the headers can be included under C++03 and C++11 and degrade gracefully (to no-ops.)  
  
Since I don't want to force every library that uses Describe (e.g. ContainerHash) to require C++14, I don't want to declare it as a C++14 library.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-05-13 12:58:26 UTC  
> Url: https://github.com/boostorg/describe/issues/51#issuecomment-2876409865  

> special case  
  
Ok.     
  
Just some ideas, up to you.    
  
- reply to the concern on the boost mailing list    
- add the information in the README file   
- and in the docs
