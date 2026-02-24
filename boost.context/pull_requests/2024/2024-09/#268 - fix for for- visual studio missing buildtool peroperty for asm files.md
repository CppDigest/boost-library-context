# #268 fix for for: visual studio missing buildtool peroperty for asm files [Merged]

> Username: Dargun  
> Created at: 2024-09-12 15:22:42 UTC  
> Updated at: 2024-12-26 07:07:09 UTC  
> Merged at: 2024-12-26 07:07:04 UTC  
> Closed at: 2024-12-26 07:07:04 UTC  
> Url: https://github.com/boostorg/context/pull/268  

There is a problem on visual studio to compile Boost::context with BOOST_CONTEXT_IMPLEMENTATION = "fcontext".  
For all asm files are no build tool defined so they will not be compiled during build of context lib and functions "jump_fcontext, make_fcontext and ontop_fcontext" are no compiled.  
The fix is to pass the defined ASM_LANGUAGE on each asm file as PROPERTY.

---

## Comment 1

> Username: olk  
> Created_at: 2024-12-26 07:07:09 UTC  
> Url: https://github.com/boostorg/context/pull/268#issuecomment-2562234356  

ty

---
