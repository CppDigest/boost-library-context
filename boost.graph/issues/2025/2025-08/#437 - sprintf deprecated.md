# #437 - sprintf deprecated [Closed]

> Username: sdarwin  
> Created at: 2025-08-21 14:48:07 UTC  
> Updated at: 2025-08-25 11:40:45 UTC  
> Closed at: 2025-08-25 11:40:45 UTC  
> Url: https://github.com/boostorg/graph/issues/437  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
```  
libs/graph/example/iohb.c:    sprintf(iformat,"%%%dd",Indwidth);  
libs/graph/example/iohb.c:    sprintf(iformat,"%%%dd",Indwidth);  
libs/graph/example/iohb.c:    sprintf(pformat,"%%%dd",Ptrwidth);  
libs/graph/example/iohb.c:    sprintf(pformat,"%%%dd",Ptrwidth);  
libs/graph/example/iohb.c:          sprintf(rformat,"%% %d.%dE",Rhswidth,Rhsprec);  
libs/graph/example/iohb.c:          sprintf(rformat,"%% %d.%df",Rhswidth,Rhsprec);  
libs/graph/example/iohb.c:       sprintf(rformat,"%%%ds",Rhswidth);  
libs/graph/example/iohb.c:         sprintf(vformat,"%% %d.%dE",Valwidth,Valprec);  
libs/graph/example/iohb.c:         sprintf(vformat,"%% %d.%df",Valwidth,Valprec);  
libs/graph/example/iohb.c:      sprintf(vformat,"%%%ds",Valwidth);  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2025-08-25 05:40:18 UTC  
> Url: https://github.com/boostorg/graph/issues/437#issuecomment-3218902618  

I just merged the fix for that one but I'll let you close it when you confirm that there are no other occurrences that need fixing.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-08-25 11:40:45 UTC  
> Url: https://github.com/boostorg/graph/issues/437#issuecomment-3219932695  

" I'll let you close it"...  Alright. Thanks. Those were the occurrences of `sprintf`.
