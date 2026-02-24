# #94 - operator-> for reference_wrapper [Open]

> Username: denzor200  
> Created at: 2021-08-02 23:15:03 UTC  
> Updated at: 2021-08-03 06:47:45 UTC  
> Url: https://github.com/boostorg/core/issues/94  

Why `boost::reference_wrapper` template class dosn't has `operator->` overloading? Is there any objective reason?  
It may be useful for some instantiations, like `boost::reference_wrapper<Object*>` or like `boost::reference_wrapper<std::shared_ptr<Object>>` to use these without casting

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-03 00:09:04 UTC  
> Url: https://github.com/boostorg/core/issues/94#issuecomment-891413014  

I suppose, the main reason is that `reference_wrapper<T>` is not intended to be a proxy for `T`.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-08-03 06:44:25 UTC  
> Url: https://github.com/boostorg/core/issues/94#issuecomment-891579743  

I suspect you're misunderstanding the request as `T* operator->` whereas it's `T& operator->`.  
  
`reference_wrapper<T>` is intended to act as a reference to `T` and has an implicit conversion `operator T&`, which enables this. It also has a forwarding `operator()` because the conversion doesn't work in this case. The request makes sense, the reason for not having `operator->` is because nobody brought it up until now.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-08-03 06:47:45 UTC  
> Url: https://github.com/boostorg/core/issues/94#issuecomment-891581508  

E.g. https://godbolt.org/z/3zx9YbY44
