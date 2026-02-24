# #296 Improve error visualization. [Merged]

> Username: andreasbuhr  
> Created at: 2025-11-18 08:50:05 UTC  
> Updated at: 2025-11-23 18:15:07 UTC  
> Merged at: 2025-11-23 18:14:55 UTC  
> Closed at: 2025-11-23 18:14:55 UTC  
> Url: https://github.com/boostorg/parser/pull/296  

If the parsed string contains tab characters on the failing line, the underlining in the error message pointed to the wrong position.  
  
This patch adds as many tab characters to the underlining as there are in the parsed line. So the underlining points to the correct position.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-11-23 18:15:07 UTC  
> Url: https://github.com/boostorg/parser/pull/296#issuecomment-3568211867  

Thanks!

---
