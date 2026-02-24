# #69 - [Feature Request] Templated lookup methods in associative containers [Closed]

> Username: Lastique  
> Created at: 2018-04-19 14:35:07 UTC  
> Updated at: 2018-06-16 09:53:59 UTC  
> Closed at: 2018-05-01 13:04:33 UTC  
> Url: https://github.com/boostorg/container/issues/69  

C++14 added `find`/`lower_bound`/`upper_bound`/`equal_range`/`count` methods that accept a templated argument instead of a value of `key_type`. This is useful if the keys can be ordered against objects of different types (e.g. when the key is `std::string` and the argument is a `std::string_view`).  
  
Please, add similar overloads to the associative containers in Boost.Container: `flat_set`, `flat_multiset`, `flat_map`, `flat_multimap`, `set`, `multiset`, `map`, `multimap`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-05-01 13:04:33 UTC  
> Url: https://github.com/boostorg/container/issues/69#issuecomment-385665956  

Better late than never, requested many years ago:  
  
https://svn.boost.org/trac10/ticket/11780  
  
Fixed in commit:  
  
https://github.com/boostorg/container/commit/48c21e3187cd95b42803efe297842b52c896090e  
  
 Many thanks for the report.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-06-15 11:51:59 UTC  
> Url: https://github.com/boostorg/container/issues/69#issuecomment-397597509  

A friendly reminder to merge this to master before 1.68.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-06-16 09:53:59 UTC  
> Url: https://github.com/boostorg/container/issues/69#issuecomment-397801735  

Thanks for the reminder, just merged.
