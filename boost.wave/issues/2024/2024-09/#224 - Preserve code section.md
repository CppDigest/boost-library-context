# #224 - Preserve code section [Closed]

> Username: Zitrax  
> Created at: 2024-09-25 12:10:29 UTC  
> Updated at: 2024-10-06 17:27:16 UTC  
> Closed at: 2024-10-06 17:27:15 UTC  
> Url: https://github.com/boostorg/wave/issues/224  

Does wave in any way support keeping a section of code unchanged, for example if I have:  
  
```c++  
#ifdef FOO  
  #define BAR  
#endif  
```  
  
and want to keep all that including the directives unevaluated. For example using a pragma around that specific block of code.  
  
Or is it possible to implement something like that with the hooks?

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-09-25 18:18:45 UTC  
> Url: https://github.com/boostorg/wave/issues/224#issuecomment-2374846425  

I think the hooks are your go-to here... perhaps a combination of [found_directive](https://www.boost.org/doc/libs/1_85_0/libs/wave/doc/class_reference_ctxpolicy.html#found_directive), [evaluated_conditional_expression](https://www.boost.org/doc/libs/1_85_0/libs/wave/doc/class_reference_ctxpolicy.html#evaluated_conditional_expression), and [skipped_token](https://www.boost.org/doc/libs/1_85_0/libs/wave/doc/class_reference_ctxpolicy.html#skipped_token)? And if you need to define a custom pragma to turn this behavior on/off the [preprocess pragma output sample](https://github.com/boostorg/wave/tree/develop/samples/preprocess_pragma_output) may be of use.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-10-03 04:23:29 UTC  
> Url: https://github.com/boostorg/wave/issues/224#issuecomment-2390487716  

If you have no further questions I may close this.

---

## Comment 3

> Username: Zitrax  
> Created at: 2024-10-06 17:27:15 UTC  
> Url: https://github.com/boostorg/wave/issues/224#issuecomment-2395517056  

I think I will manage with what you said, thank you. I did some simplified testing that looked good. So I'll close it.
