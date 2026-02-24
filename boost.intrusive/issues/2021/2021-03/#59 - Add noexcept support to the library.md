# #59 - Add noexcept support to the library [Closed]

> Username: impugachev  
> Created at: 2021-03-09 12:56:06 UTC  
> Updated at: 2021-04-19 09:28:22 UTC  
> Closed at: 2021-04-19 09:18:07 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59  

Why doesn't the library use `noexcept` anywhere? I work in an environment with no exceptions, so intrusive containers are exactly what I need, but when using Boost.Intrusive, there are some problems due to the complete lack of `noexcept`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-18 12:13:12 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59#issuecomment-821982160  

The reason is that Intrusive was written long before noexcept was available. Any hint on which operations are most needed to be noexcept?

---

## Comment 2

> Username: impugachev  
> Created at: 2021-04-18 14:48:49 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59#issuecomment-822003304  

First of all, hook constructors. Because of them, when inheriting, you have to explicitly write an empty noexcept constructor.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2021-04-18 15:59:59 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59#issuecomment-822014846  

Thanks for the feedback. I plan to implement firs noexcept support for the next release.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2021-04-19 09:18:59 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59#issuecomment-822314204  

Thanks for the report. Let me know if the support is enough or I missed something important.

---

## Comment 5

> Username: impugachev  
> Created at: 2021-04-19 09:28:22 UTC  
> Url: https://github.com/boostorg/intrusive/issues/59#issuecomment-822320429  

Many thanks! It looks like everything I need is here. I'll write if I find anything else while using it.
