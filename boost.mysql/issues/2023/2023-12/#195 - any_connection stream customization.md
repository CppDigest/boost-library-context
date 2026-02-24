# #195 - any_connection stream customization [Closed]

> Username: anarthal  
> Created at: 2023-12-20 12:15:58 UTC  
> Updated at: 2024-08-10 16:24:32 UTC  
> Closed at: 2024-08-10 16:24:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/195  

The new `any_connection` doesn't allow custom `Stream` types - it always uses `detail::variant_stream`. I'm not sure whether this is really required, since this stream is designed to cover almost all real use cases.  
  
If your use case doesn't fit the default stream, please comment in this issue.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:24:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/195#issuecomment-2282200535  

I strongly believe allowing this is a mistake. If you strongly need this, please comment.
