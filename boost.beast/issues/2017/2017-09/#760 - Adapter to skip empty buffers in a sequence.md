# #760 - Adapter to skip empty buffers in a sequence [Closed]

> Username: vinniefalco  
> Created at: 2017-09-01 01:02:28 UTC  
> Updated at: 2022-06-16 16:31:30 UTC  
> Closed at: 2022-06-16 16:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/760  

We can add an adapter which skips empty buffers in a buffer sequence. This is helpful for zlib which can only work on the first non-empty buffer in a sequence. It is also helpful for the serializer, which sometimes creates sequences with some empty buffers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/760#issuecomment-1157881862  

ugh no more buffer sequence adaptors!!!!
