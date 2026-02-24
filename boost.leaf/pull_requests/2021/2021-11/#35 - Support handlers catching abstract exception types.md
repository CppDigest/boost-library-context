# #35 Support handlers catching abstract exception types. [Closed]

> Username: vector-of-bool  
> Created at: 2021-11-24 04:27:14 UTC  
> Updated at: 2021-11-24 07:06:07 UTC  
> Closed at: 2021-11-24 07:06:06 UTC  
> Url: https://github.com/boostorg/leaf/pull/35  

- Because it is impossible to create an instance of an abstract type, we  
  do not need a slot for that type. Overload the error_type to be  
  `void`.  
- For `check()` and `get()`, ignore the error value tuple and just  
  inspect the in-flight exception only.

---

## Comment 1

> Username: zajo  
> Created_at: 2021-11-24 06:39:38 UTC  
> Url: https://github.com/boostorg/leaf/pull/35#issuecomment-977576630  

Thank you very much for the contribution, indeed catching abstract exception types should be supported. However, there is a simpler way to do this, see https://github.com/boostorg/leaf/commit/27ef3e8bcda495662bcf9c9bec773bb9d3cf4d0c. Let me know if I've missed something.

---

## Comment 2

> Username: vector-of-bool  
> Created_at: 2021-11-24 06:56:14 UTC  
> Url: https://github.com/boostorg/leaf/pull/35#issuecomment-977584736  

You're solution is indeed simpler. I suppose I don't know the codebase well enough that I could get to that point :slightly_smiling_face:. Feel free to close this.

---

## Comment 3

> Username: zajo  
> Created_at: 2021-11-24 07:06:06 UTC  
> Url: https://github.com/boostorg/leaf/pull/35#issuecomment-977590062  

I know you've read a lot of the code, you clearly have a very good understanding of LEAF, and it is complex. Thank you!

---
