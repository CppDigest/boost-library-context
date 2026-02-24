# #1440 - Add buffers_empty [Closed]

> Username: vinniefalco  
> Created at: 2019-02-08 05:26:50 UTC  
> Updated at: 2019-04-19 01:41:34 UTC  
> Closed at: 2019-04-19 01:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1440  

`buffers_empty(b)` can produce a result faster than `buffer_size(b) == 0`

---

## Comment 1

> Username: djarek  
> Created at: 2019-02-08 20:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1440#issuecomment-461932101  

Will this be a customization point?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-08 20:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1440#issuecomment-461935953  

I'd rather not have a customization point. I'm not sure if `buffers_empty` is even a good idea.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 01:39:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1440#issuecomment-484741187  

I see what you mean about a customization point. I have `detail::buffers_empty` now, but I am hesitant to make it public.
