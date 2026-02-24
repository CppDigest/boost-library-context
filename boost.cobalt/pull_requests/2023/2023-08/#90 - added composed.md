# #90 added composed. [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-22 09:25:58 UTC  
> Updated at: 2024-12-13 02:24:10 UTC  
> Closed at: 2024-12-13 02:24:10 UTC  
> Url: https://github.com/boostorg/cobalt/pull/90  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-08-24 08:16:17 UTC  
> Url: https://github.com/boostorg/cobalt/pull/90#issuecomment-1691223152  

Downgrading to draft. We'll need internal use-cases to make this relevant.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-04-20 11:39:49 UTC  
> Url: https://github.com/boostorg/cobalt/pull/90#issuecomment-2067647821  

here is what I am now thinking:  
  
```cpp  
extern promise<int> dummy();  
composition<error_code, size_t> compost(asio::ip::tcp::socket & sock) // take the executor from the first argument, also works if it's this!  
{  
   auto [ec, n] = co_await sock.async_read_some(sock, ...); // automatically apply as_tuple, to discourage exceptions being thrown!  
   auto [ex, i] = co_await dummy(); // ex-ptr here  
  
   co_return {ec, n}; // complete  
}  
```  
  
so let's say we have  
  
```cpp  
extern composition<error_code, mysql::resultset> mysql::query(mysql::connection & conn, string_view query);  
  
// in promise:  
  
auto rs = co_await mysql::query(conn, "select * from cities"); // throws  
  
auto [ec, rr] = co_await cobalt::as_tuple(mysql::query(conn, "select * from cities")); // now you get the ec instead of an ep here  
```

---
