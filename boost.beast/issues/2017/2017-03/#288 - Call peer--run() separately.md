# #288 - Call peer::run() separately [Closed]

> Username: vinniefalco  
> Created at: 2017-03-28 01:34:13 UTC  
> Updated at: 2017-06-21 02:49:02 UTC  
> Closed at: 2017-06-21 02:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/288  

Instead of   
```  
peer{*this, ep_, std::move(sock_)};  
```  
  
We should use  
```  
peer p{*this, ep_, std::move(sock_)};  
p.run();  
```  
  
In websocket examples (and HTTP). This would make it easier for readers to understand.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 02:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/288#issuecomment-309947772  

*server-framework* does this now
