# #333 Update sanitizers support documentation [Open]

> Username: mathbunnyru  
> Created at: 2025-11-14 15:38:39 UTC  
> Updated at: 2025-11-14 15:42:51 UTC  
> Url: https://github.com/boostorg/fiber/pull/333  

Fix: https://github.com/boostorg/fiber/issues/289  
Clarify instructions for using AddressSanitizer with Boost.Context.

---

## Review 1 [Commented]

> Username: mathbunnyru  
> Created_at: 2025-11-14 15:39:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fiber/pull/333#pullrequestreview-3465506223  

📁 doc/stack.qbk

```diff
 295 | Sanitizers (GCC/Clang) are confused by the stack switches.
 296 | The library (and Boost.Context too) is required to be compiled with property (b2 command-line)
 297 |- `context-impl=ucontext` and compilers santizer options.
```

> Username: mathbunnyru  
> Created_at: 2025-11-14 15:39:20 UTC  
> Url: https://github.com/boostorg/fiber/pull/333#discussion_r2527938777  

`santizer` -> `sanitizer` (typo)  
compilers -> compiler (it's 1 compiler at a time)


---

## Review 2 [Commented]

> Username: mathbunnyru  
> Created_at: 2025-11-14 15:40:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fiber/pull/333#pullrequestreview-3465508793  

📁 doc/stack.qbk

```diff
 297 |- `context-impl=ucontext` and compilers santizer options.
 298 |- Users must define `BOOST_USE_ASAN` before including any Boost.Context headers
 299 |- when linking against Boost binaries.
```

> Username: mathbunnyru  
> Created_at: 2025-11-14 15:40:02 UTC  
> Url: https://github.com/boostorg/fiber/pull/333#discussion_r2527940864  

This is technically incorrect because there are no defines "when linking against Boost binaries"


---
