# #70 - The `generator` example [Open]

> Username: akrzemi1  
> Created at: 2023-08-13 12:43:05 UTC  
> Updated at: 2023-08-13 12:58:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/70  

In the `generator` example in the docs (https://klemens.dev/async/#generator), where is the instruction that prints "In main 4"? I do not see any explicit loop and only one `printf`. Or is it just a bug?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-08-13 12:52:41 UTC  
> Url: https://github.com/boostorg/cobalt/issues/70#issuecomment-1676351268  

Also, the second generator example says in the output, "Pushed 2" but there is no corresponding print instruction.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-13 12:58:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/70#issuecomment-1676352527  

Yeah, that is a mistakez there should be a second printf.
