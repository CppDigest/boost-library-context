# #232 - semantic action failures [Closed]

> Username: psiha  
> Created at: 2025-05-05 12:26:20 UTC  
> Updated at: 2026-02-22 03:43:33 UTC  
> Closed at: 2026-02-22 03:43:33 UTC  
> Url: https://github.com/boostorg/parser/issues/232  

When executing a semantic action I can, well, find a semantic error in the input (e.g. a thing referenced by the input does not exist while the input itself is syntactically correct) - in which case I'd like to throw an error and have the same builtin mechanism as for expectations catch and handle it (i.e. where the input location/context information is still available)...  
(currently I 'hack' this by throwing parse_error exceptions)

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 20:21:23 UTC  
> Url: https://github.com/boostorg/parser/issues/232#issuecomment-3066020017  

I don't consider that a hack.  That sounds like a completely reasonable way to do what you want.  Actions already have a built-in failure mechanism -- you just do `pass_(ctx) = false`.  I'm not getting what the feature is that you're requesting.
