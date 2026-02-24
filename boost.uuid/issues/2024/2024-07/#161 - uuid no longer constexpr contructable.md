# #161 - uuid no longer constexpr contructable [Closed]

> Username: tobias-loew  
> Created at: 2024-07-26 14:30:28 UTC  
> Updated at: 2024-10-12 19:00:00 UTC  
> Closed at: 2024-10-12 19:00:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/161  

Before version 1.85 uuid was an aggreagate and could be constexpr constructed from a sequence of bytes.  
With version 1.85 this is no longer possible.  
(I've got user-defined literal which converts uuid string-representation at compile-time into uuids that no longer works with 1.85.)

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-26 16:18:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2253084356  

Indeed, thanks for the report. I'll see if I can fix this between the beta and the release, as it's not as easy as just adding a `constexpr` to the constructor because `std::memcpy` isn't always `constexpr`.

---

## Comment 2

> Username: tobias-loew  
> Created at: 2024-07-29 06:50:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2255075974  

Another minor regression: before `.data` returned a reference to the array. Migration would be easier having a `data()` method returning a reference or a `std::span` to the byte representation.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-07-29 17:05:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2256459134  

For a contiguous container, `data()` by convention returns a pointer to the first element. It's in principle possible to return a reference to the array which will decay to a pointer but that's a bit too inventive.  
  
Although maybe if the constructor is constexpr, data() should be, too.

---

## Comment 4

> Username: Osyotr  
> Created at: 2024-08-21 14:27:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2302189293  

It's also no longer trivial: https://godbolt.org/z/sb8K6YTz7  
Probably because of this line: https://github.com/boostorg/uuid/blob/c432e16a4c6b477a1f1e0bbdc6de806237b0c4f0/include/boost/uuid/uuid.hpp#L96

---

## Comment 5

> Username: pdimov  
> Created at: 2024-08-21 18:36:55 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2302722713  

It's still trivially copyable and standard layout, but no longer trivial (because the default constructor doesn't leave it uninitialized) or POD.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-09-22 23:37:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2367019136  

Construction should be constexpr now on develop.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-10-12 19:00:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/161#issuecomment-2408661729  

Has been merged to master.
