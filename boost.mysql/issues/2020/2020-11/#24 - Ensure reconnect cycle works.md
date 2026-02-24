# #24 - Ensure reconnect cycle works [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:44:09 UTC  
> Updated at: 2021-03-09 07:25:29 UTC  
> Closed at: 2021-03-09 07:25:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/24  

Ensure you can reconnect after a failed handshake/successful close.

---

## Comment 1

> Username: anarthal  
> Created at: 2021-03-07 21:14:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/24#issuecomment-792354477  

I don't think this is possible with asio sockets, as they have to be re-created once they are closed.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-03-07 21:38:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/24#issuecomment-792357934  

You can close and re-open an asio socket. Under the covers it’s just a socket descriptor.  
What did you try?

---

## Comment 3

> Username: anarthal  
> Created at: 2021-03-08 18:03:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/24#issuecomment-792956111  

By bad, I was convinced of the opposite. There is actually a problem with the reconnect cycle, requires further investigation.

---

## Comment 4

> Username: anarthal  
> Created at: 2021-03-08 22:29:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/24#issuecomment-793130407  

Fixed in branch `reconnect-cycle` - will merge tomorrow after CI builds.
