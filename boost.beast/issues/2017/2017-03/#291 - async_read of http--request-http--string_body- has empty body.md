# #291 - [http branch] async_read of http::request<http::string_body> has empty body [Closed]

> Username: mika-fischer  
> Created at: 2017-03-30 14:20:36 UTC  
> Updated at: 2017-04-10 23:54:40 UTC  
> Closed at: 2017-04-10 23:54:40 UTC  
> Url: https://github.com/boostorg/beast/issues/291  

Reading the whole request with a `message_parser<true, string_body, fields>` works fine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-03-31 20:39:08 UTC  
> Url: https://github.com/boostorg/beast/issues/291#issuecomment-290823828  

You gave me a panic, but then I saw it was in the http branch!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-03-31 20:45:52 UTC  
> Updated at: 2017-03-31 22:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/291#issuecomment-290825324  

I pushed this fix to the branch:  
https://github.com/vinniefalco/Beast/commit/c8986c91b0ea1e0d8f448eb4f924e5e06f76da6d

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-10 23:54:40 UTC  
> Url: https://github.com/boostorg/beast/issues/291#issuecomment-293111065  

This should be fixed now, thanks
