# #760 - Copy/alias std::iter_value_t in implementation of detail::value_type [Closed]

> Username: grisumbras  
> Created at: 2022-09-29 08:40:16 UTC  
> Updated at: 2022-10-19 13:59:59 UTC  
> Closed at: 2022-10-19 13:59:59 UTC  
> Url: https://github.com/boostorg/json/issues/760  

The main problem with `detail::value_type` is that it does not use `iterator_traits::value_type`. This is not an issue with most iterators, but is definitely an issue with proxy iterators (e.g. `std::vector<bool>::iterator`). Standard Ranges library uses a multi-step approach to determine the appropriate value type of a range. We can copy (most of) it and even alias it in C++20.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-29 14:26:44 UTC  
> Url: https://github.com/boostorg/json/issues/760#issuecomment-1262363597  

Where do we need to use `iter_value`?

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-10-17 14:54:16 UTC  
> Url: https://github.com/boostorg/json/issues/760#issuecomment-1280995721  

When we use `detail::value_type`, that is for conversions.
