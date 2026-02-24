# #123 Asio bad_address_cast fix under no exceptions [Closed]

> Username: kvankooten  
> Created at: 2018-06-14 18:21:46 UTC  
> Updated at: 2020-12-30 02:08:47 UTC  
> Closed at: 2020-12-30 02:08:47 UTC  
> Url: https://github.com/boostorg/asio/pull/123  

When using the Asio module in MSVC when exceptions are disabled, deriving an exception from bad_cast throws a linker error. A simple fix would look similar to the lexical_cast, bad_any_cast, etc.  
  
Linked to bug #13607

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:08:47 UTC  
> Url: https://github.com/boostorg/asio/pull/123#issuecomment-752301934  

Fixed in asio 1.12.2 / boost 1.69.

---
