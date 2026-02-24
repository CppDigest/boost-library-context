# #3041 - Is `keep_alive` intentionally not implemented for `header`? [Open]

> Username: inetic  
> Created at: 2025-10-09 08:33:08 UTC  
> Updated at: 2025-10-10 05:36:00 UTC  
> Url: https://github.com/boostorg/beast/issues/3041  

The `keep_alive` function is implemented for `message` but it only requires information from the `header`. Due to this, any function that composes a response header needs to take the whole `message` unnecessarily.  
  
Our work around is to just re-implement the `keep_alive` function and use that, but I'm wondering if there is a reason I'm missing for the exclusion or whether is just hasn't been a priority to have it in `header`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-10-09 14:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/3041#issuecomment-3386192605  

The implementation is actually in `header`. I don't know why it is in `message`. Try patching Beast and see if it works? Maybe we could merge it.

---

## Comment 2

> Username: ashtum  
> Created at: 2025-10-09 17:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/3041#issuecomment-3386958164  

I think the reason might be that `request_header` and `response_header` are two separate classes, and `keep_alive` is common to both request and response messages. Moving it to the header would lead to code duplication. As for why it is not in `basic_fields`, it might be to keep `basic_fields` limited to functioning as a container.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-10-09 21:39:40 UTC  
> Url: https://github.com/boostorg/beast/issues/3041#issuecomment-3387572222  

the implementation of `keep_alive()` depends on knowing if the message is a request or a response, which is not possible in `basic_fields`.

---

## Comment 4

> Username: ashtum  
> Created at: 2025-10-10 05:36:00 UTC  
> Url: https://github.com/boostorg/beast/issues/3041#issuecomment-3388357432  

> the implementation of `keep_alive()` depends on knowing if the message is a request or a response, which is not possible in `basic_fields`.  
  
I think `keep_alive` is a common field in both request and response and the implementation doesn't check for that either. However, it requires the message version, which is only available in `request_header` and `response_header`:    
https://github.com/boostorg/beast/blob/9b24b28d502946583300d3e58767cb30a8ec8486/include/boost/beast/http/impl/fields.hpp#L817
