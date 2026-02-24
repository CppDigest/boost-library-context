# #182 - Add `boost::integer_sequence` and `boost::make_index_sequence` [Closed]

> Username: denzor200  
> Created at: 2024-12-26 21:12:03 UTC  
> Updated at: 2024-12-27 02:14:27 UTC  
> Closed at: 2024-12-26 22:30:15 UTC  
> Url: https://github.com/boostorg/core/issues/182  

C++11 users need analogues of `std::integer_sequence`, `std::make_index_sequence` and so other template helpers for it.  
https://en.cppreference.com/w/cpp/utility/integer_sequence

---

## Comment 1

> Username: Lastique  
> Created at: 2024-12-26 22:30:15 UTC  
> Url: https://github.com/boostorg/core/issues/182#issuecomment-2563140637  

There is an implementation in [Boost.MP11](https://www.boost.org/doc/libs/1_87_0/libs/mp11/doc/html/mp11.html#integer_sequence).

---

## Comment 2

> Username: pdimov  
> Created at: 2024-12-27 00:21:34 UTC  
> Url: https://github.com/boostorg/core/issues/182#issuecomment-2563189487  

We also [have one in Compat](https://www.boost.org/doc/libs/1_87_0/libs/compat/doc/html/compat.html#integer_sequence).

---

## Comment 3

> Username: Lastique  
> Created at: 2024-12-27 02:10:59 UTC  
> Url: https://github.com/boostorg/core/issues/182#issuecomment-2563238386  

> We also [have one in Compat](https://www.boost.org/doc/libs/1_87_0/libs/compat/doc/html/compat.html#integer_sequence).  
  
I'm curious, what was the reason for adding a copy in Boost.Compat?

---

## Comment 4

> Username: pdimov  
> Created at: 2024-12-27 02:14:25 UTC  
> Url: https://github.com/boostorg/core/issues/182#issuecomment-2563239984  

To not depend on mp11 (which isn't allowed.)
