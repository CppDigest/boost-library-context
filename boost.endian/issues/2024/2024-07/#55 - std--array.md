# #55 - std::array [Open]

> Username: parchinskiy  
> Created at: 2024-07-20 14:30:02 UTC  
> Updated at: 2024-07-21 10:42:11 UTC  
> Url: https://github.com/boostorg/endian/issues/55  

Hi, since C++03 is no longer supported, is there any reason not to add an overload for std::array [here](https://github.com/boostorg/endian/blob/develop/include/boost/endian/detail/endian_reverse.hpp#L164)?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-20 22:02:00 UTC  
> Url: https://github.com/boostorg/endian/issues/55#issuecomment-2241306923  

I suppose it makes sense. `<array>` is a surprisingly heavy header to include, though, because it often includes `<algorithm>`, so this is one possible drawback.

---

## Comment 2

> Username: parchinskiy  
> Created at: 2024-07-21 10:25:44 UTC  
> Updated at: 2024-07-21 10:31:48 UTC  
> Url: https://github.com/boostorg/endian/issues/55#issuecomment-2241558100  

@pdimov Maybe instead of overloading for an array, it makes sense for functions like big_to_native_in_place, native_to_big_in_place, etc. to add overloads with iterators? Then you will not need to include `<array>` and the number of supported types will increase.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-07-21 10:42:10 UTC  
> Url: https://github.com/boostorg/endian/issues/55#issuecomment-2241562369  

Yes, I was wondering whether supporting just `std::array` was justified, as I assume `std::vector` support would also be legitimate. And there are also `std::pair` and `std::tuple`, for which reverse in place also makes sense.  
  
It's in principle possible to do what ContainerHash does and support all containers and tuple-likes by default, although that's somewhat outside the current scope of the Endian library. (We can even make described structs work out of the box.)  
  
Out of curiosity, what is you current use case for supporting `std::array`? Do you need support for other containers, or for pair/tuple?
