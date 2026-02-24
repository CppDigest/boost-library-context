# #1132 - websocket::stream::write overloads for dynamic buffer [Open]

> Username: vinniefalco  
> Created at: 2018-05-17 17:46:42 UTC  
> Updated at: 2022-09-24 06:17:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1132  

We can add stream `write` and `async_write` overloads for dynamic buffers, where the input sequence is sent and also consumed. These will require the `is_dynamic_buffer` constraint.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:17:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1132#issuecomment-1256877819  

Are you sure the call to `.data()` is too much?
