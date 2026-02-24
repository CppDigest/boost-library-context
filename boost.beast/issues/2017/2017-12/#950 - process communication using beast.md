# #950 - process communication using beast [Closed]

> Username: mozeat-sun  
> Created at: 2017-12-24 13:47:08 UTC  
> Updated at: 2017-12-24 13:59:01 UTC  
> Closed at: 2017-12-24 13:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/950  

hi Vinnie   
       i try to use http protocol  for communications between processes . which http server example may do well, could both async and sync work together  ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-24 13:54:51 UTC  
> Url: https://github.com/boostorg/beast/issues/950#issuecomment-353785578  

You need a server in one process, and a client in the other. It does not matter what kind of server or client you use, they should all work together.

---

## Comment 2

> Username: mozeat-sun  
> Created at: 2017-12-24 13:58:55 UTC  
> Url: https://github.com/boostorg/beast/issues/950#issuecomment-353785772  

ok, thanks
