# #55 - Hooks not called for expanding specific predefined macros [Closed]

> Username: jefftrull  
> Created at: 2020-01-12 04:13:08 UTC  
> Updated at: 2020-04-12 19:40:04 UTC  
> Closed at: 2020-04-12 19:40:04 UTC  
> Url: https://github.com/boostorg/wave/issues/55  

This is a port of [TRAC 10733](https://svn.boost.org/trac10/ticket/10733) by @tberghammer. The original text reads:  
  
>Non of the preprocessor hook is called during the expansion of the following predefined macros: `__LINE__`, `__FILE__`, `__INCLUDE_LEVEL__`.  
>  
>This is because the expansion of these macros are handled with a specific code path which skips the hooks.  
>  
>Expected behavior:  
>  
>- expanding_object_like_macro hook is called before the expansion  
>- expansion happens based on the return value from the hook  
>- expanded_macro hook is called after the expansion

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-01-12 04:14:24 UTC  
> Updated at: 2020-01-12 04:14:32 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-573380475  

I verified this issue still exists by using the attached testcase.  
[trac10733.cpp](https://github.com/boostorg/wave/files/4050196/trac10733.cpp.txt)

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-03-04 18:45:18 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-594743055  

I wonder if `__VA_OPT__()` should be traced also

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-03-04 19:04:43 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-594757606  

> I wonder if __VA_OPT__() should be traced also  
  
Would we have to trace `__VA_ARGS__` in this case as well? What other macros need to be handled?

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-03-04 19:27:22 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-594775942  

Well, we wouldn't _have_ to trace it :) but I think maybe for consistency, yes. The others are listed above - basically anything with special-case handling (i.e. not via `cpp_macromap_predef.hpp`).

---

## Comment 5

> Username: jefftrull  
> Created at: 2020-03-17 20:25:55 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600280276  

@hkaiser for `__VA_ARGS__` what does it mean to "rescan"? I can understand when to call `expanding_macro` and `expanded_macro` but currently rescanning only happens for the parent variadic macro. Should we contrive to call the rescan hook anyway, somehow?

---

## Comment 6

> Username: hkaiser  
> Created at: 2020-03-17 20:50:15 UTC  
> Updated at: 2020-03-17 20:50:54 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600290315  

> @hkaiser for `__VA_ARGS__` what does it mean to "rescan"? I can understand when to call expanding_macro and expanded_macro but currently rescanning only happens for the parent variadic macro. Should we contrive to call the rescan hook anyway, somehow?  
  
@jefftrull I think that the only difference between a 'normal' argument of a function-like macro and `__VA_ARGS__` is that the latter refers to the overall token sequence represented by all parameters that are matched by the ellipses. All parameters that match the ellipses are expanded before being inserted into the replacement list in the same way as any parameter matching a non-variadic argument is expanded and inserted into the replacement list. Rescanning is nothing specific to `__VA_ARGS__` as it affects the replacement list after all arguments have been handled.   
  
In this sense `__VA_ARGS__` is not a macro on it's own, it is a predefined name that refers to all parameters matched by the ellipses of a variadic function-like macro.

---

## Comment 7

> Username: jefftrull  
> Created at: 2020-03-17 20:51:47 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600290967  

OK! But what should we do with it, then...? Skip the rescan hook? Skip all the hooks? I'm not sure how to proceed.

---

## Comment 8

> Username: hkaiser  
> Created at: 2020-03-17 20:54:34 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600293033  

Let's not treat `__VA_ARGS__` as a macro to begin with. We're not treating any parameter of a function-like macro as a macro itself (i.e. in `#define FOO(x) x`, `x` is not treated as a macro either).

---

## Comment 9

> Username: jefftrull  
> Created at: 2020-03-17 21:19:29 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600306729  

OK! How do you feel about `__VA_OPT__`? We could call function-like macro hooks there...

---

## Comment 10

> Username: hkaiser  
> Created at: 2020-03-17 21:47:01 UTC  
> Url: https://github.com/boostorg/wave/issues/55#issuecomment-600316914  

> OK! How do you feel about __VA_OPT__? We could call function-like macro hooks there...  
  
Yes, `__VA_OPT__()` is explicitly defined as a function-like macro. We could call the hooks for it.
