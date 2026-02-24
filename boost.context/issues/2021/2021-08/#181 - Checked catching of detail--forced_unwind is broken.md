# #181 - Checked catching of detail::forced_unwind is broken [Closed]

> Username: yuri-kilochek  
> Created at: 2021-08-05 19:40:24 UTC  
> Updated at: 2021-08-06 05:37:16 UTC  
> Closed at: 2021-08-06 05:37:15 UTC  
> Url: https://github.com/boostorg/context/issues/181  

Current implementation of this feature relies on copy elision in `throw` expression. Such elision is not mandatory, and when it doesn't happen, `forced_unwind::caught` remains `false` when the temporary is destroyed, which incorrectly triggers the assert.  
  
I recommend removing the checking entirely.

---

## Comment 1

> Username: olk  
> Created at: 2021-08-06 05:37:15 UTC  
> Url: https://github.com/boostorg/context/issues/181#issuecomment-894014155  

fixed, ty
