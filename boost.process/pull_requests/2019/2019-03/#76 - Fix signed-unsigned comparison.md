# #76 Fix signed/unsigned comparison [Merged]

> Username: orgads  
> Created at: 2019-03-20 10:17:07 UTC  
> Updated at: 2019-03-23 17:46:21 UTC  
> Merged at: 2019-03-22 15:56:14 UTC  
> Closed at: 2019-03-22 15:56:14 UTC  
> Url: https://github.com/boostorg/process/pull/76  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2019-03-20 15:55:25 UTC  
> Url: https://github.com/boostorg/process/pull/76#issuecomment-474898800  

Thanks but I fear other tools will then complain about the C-style cast. Will add a static cast there.

---

## Comment 2

> Username: orgads  
> Created_at: 2019-03-20 15:59:48 UTC  
> Updated_at: 2019-03-20 16:00:14 UTC  
> Url: https://github.com/boostorg/process/pull/76#issuecomment-474901123  

That's not C-Style, it's function-style, which is C++.  
  
But if you prefer static_cast I'm fine with it.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2019-03-21 05:05:16 UTC  
> Url: https://github.com/boostorg/process/pull/76#issuecomment-475114286  

You're right, but a compiler might still warn about this. I personally don't mind that for brevity sake. But you know, you'd replace one warning with another warning. If you change it, I'll merge it, otherwise I'll add it in the next days myself. Thanks!

---

## Comment 4

> Username: orgads  
> Created_at: 2019-03-21 21:41:06 UTC  
> Url: https://github.com/boostorg/process/pull/76#issuecomment-475413822  

I don't know any compiler that warns about it. It's just like any other ctor call. But I suppose you'd want static_cast anyway for consistency, so there you have it :)

---
