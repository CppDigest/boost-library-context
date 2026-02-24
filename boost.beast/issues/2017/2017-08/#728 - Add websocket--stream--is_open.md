# #728 - Add websocket::stream::is_open [Closed]

> Username: vinniefalco  
> Created at: 2017-08-10 03:03:30 UTC  
> Updated at: 2017-09-01 03:31:13 UTC  
> Closed at: 2017-09-01 03:31:13 UTC  
> Url: https://github.com/boostorg/beast/issues/728  

Rename `failed_` to `open_` and invert the logic everywhere  
  
`is_open` can just return `! open_`. This is a replacement for the request feature to add `bool closed()`.  
  
Operations should check `if(! open_)` and return `websocket::error::invalid`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:31:13 UTC  
> Url: https://github.com/boostorg/beast/issues/728#issuecomment-326478476  

Done as of **version 111**
