# #230 - transform with access to context [Closed]

> Username: psiha  
> Created at: 2025-05-05 12:10:36 UTC  
> Updated at: 2025-07-12 20:18:00 UTC  
> Closed at: 2025-07-12 20:18:00 UTC  
> Url: https://github.com/boostorg/parser/issues/230  

I have a need for attribute type transformation which requires state (i.e. access to _globals( ctx ), imagine an integral ID that needs to be converted to an object reference) - would it be a big deal to add this 'complication'?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 20:18:00 UTC  
> Url: https://github.com/boostorg/parser/issues/230#issuecomment-3066018330  

I don't want to mess with the current transform design.  I think this is best done with a custom action.
