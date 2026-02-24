# #66 Added dynamic_bitset(size_type, unsigned long long, const Allocator&) & to_ullong() and fixed typo [Closed]

> Username: akr-akari  
> Created at: 2022-04-27 03:29:55 UTC  
> Updated at: 2022-05-03 02:52:08 UTC  
> Closed at: 2022-05-03 02:52:08 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/66  

The constructor of std::bitset is unsigned long long since C++11, and a new member function `to_ullong()` has also been added. I know this library needs to be compatible with C++03, but I think it can be solved with the macro `BOOST_NO_LONG_LONG`.  
Thanks!

---

## Comment 1

> Username: jeking3  
> Created_at: 2022-04-27 12:03:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/66#issuecomment-1110917801  

Need time to think about the implications of this change.  If one wanted to align it with C++11 I would expect a couple compile-time branches ( checking the C++ level ) for something like a `value_size` typedef and then use that liberally in place of `unsigned long` or `unsigned long long`.  Also need to think about whether that constitutes a breaking change in any situation.

---
