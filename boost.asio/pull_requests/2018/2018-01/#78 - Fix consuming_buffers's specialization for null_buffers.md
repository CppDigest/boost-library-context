# #78 Fix consuming_buffers's specialization for null_buffers [Closed]

> Username: ichramm  
> Created at: 2018-01-16 21:08:12 UTC  
> Updated at: 2020-12-30 02:13:30 UTC  
> Closed at: 2020-12-30 02:13:30 UTC  
> Url: https://github.com/boostorg/asio/pull/78  

The specialization for null_buffers has a typo on the name of the method `total_consumed`, it misses the final "d".

---

## Comment 1

> Username: ichramm  
> Created_at: 2018-01-16 22:08:50 UTC  
> Url: https://github.com/boostorg/asio/pull/78#issuecomment-358124012  

Ticket [#13401](https://svn.boost.org/trac10/ticket/13401)

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:13:30 UTC  
> Url: https://github.com/boostorg/asio/pull/78#issuecomment-752302663  

Fixed in asio 1.12.2 / boost 1.69.

---
