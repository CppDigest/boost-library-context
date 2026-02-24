# #226 - Compile-time trace mode [Closed]

> Username: psiha  
> Created at: 2025-04-22 09:41:57 UTC  
> Updated at: 2025-08-07 12:12:50 UTC  
> Closed at: 2025-04-26 22:24:21 UTC  
> Url: https://github.com/boostorg/parser/issues/226  

Please make the trace mode parameter actually and fully compile-time - as it actually already is internally. Save on compile-time and binary size. Who actually needs it to be a runtime parameter can always make the wrapper you already made internally.  
(Not true that that would require a full recompilation to change the option/template parameter if it is set in a single TU)

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:24:21 UTC  
> Url: https://github.com/boostorg/parser/issues/226#issuecomment-2832663679  

I won't be making this change.

---

## Comment 2

> Username: psiha  
> Created at: 2025-05-05 11:02:20 UTC  
> Url: https://github.com/boostorg/parser/issues/226#issuecomment-2850638994  

Am I wrong in thinking/'detecting' that you already do/have this internally and so just won't make it public?

---

## Comment 3

> Username: andreasbuhr  
> Created at: 2025-08-07 12:12:50 UTC  
> Url: https://github.com/boostorg/parser/issues/226#issuecomment-3163933482  

Here is the patch you (and I) need:  
https://github.com/boostorg/parser/pull/267
