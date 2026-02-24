# #1266 - async_read_some trick to reduce instantiations [Closed]

> Username: vinniefalco  
> Created at: 2018-10-11 17:27:41 UTC  
> Updated at: 2022-06-16 21:04:12 UTC  
> Closed at: 2022-06-16 16:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1266  

Instead of using `post`, a composed operation can use `async_read_some` with a zero-length buffer sequence. This will reduce the number of template instantiations, resulting in smaller code, for the case where the composed operation is already calling `async_read_some` elsewhere.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1266#issuecomment-1157874669  

We aren't doing this anymore

---

## Comment 2

> Username: sehe  
> Created at: 2022-06-16 21:04:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1266#issuecomment-1158129216  

I'm glad. It seemed like a leaky hack that could throw off certain kinds of streams (think of decompressing streams)
