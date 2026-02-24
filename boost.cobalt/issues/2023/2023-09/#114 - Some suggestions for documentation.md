# #114 - Some suggestions for documentation [Open]

> Username: akrzemi1  
> Created at: 2023-09-16 00:01:33 UTC  
> Updated at: 2023-09-16 00:01:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/114  

- [ ] The initial description of `promise` says:  
      "Promises can also be used to spawn tasks easily."  
  
  Could the docs illustrate what this means?  
  
- [ ] When we are first exposed, in overview, to promises, we read:  
     "Promises are by default attached. This means, that a cancellation is sent when the `promise` handles goes out of scope."  
  Maybe this concept of being "attached" could be described in more detail in the Design seciton?   
  Does "attached"/"detached" property only apply `promise`? Word "attached" requires an object. I suppose, a `promise` is attached to a coroutine.  
  
- [ ] Docs for `generator::get()` say:  
  "// Get the return value. Throws if not ready."  
  1. "not ready" imprecise. I think you should define a term "has a ready value", define it for `generator` and then use the term to define bth `get()` and `ready()`.  
  2. It says it throw, but it doesn't say what the type and the properties of the exception are.  
  3. Why does it throw rathe than being UB? The policy for signlling logic errors should be described in the Design section.  
  
- [ ] Docs for `task` say:  
   "Unlike a [promise](https://klemens.dev/async/reference.html#promise), a task can be awaited or spawned on another executor than it was created on."   
  Again, we don't know what this spannig is.  
  
- [ ] The Outline for `task` implies it cannot be canceled. Is this true?  
  
- [ ] Docs for `detached` say:  
    "Promises are mainly used to spawn tasks easily."  
    Change "Promises are" to "`detached` is".
