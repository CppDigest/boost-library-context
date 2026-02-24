# #20 - Iterator header is now deprecated [Closed]

> Username: amosialek  
> Created at: 2020-12-30 11:55:46 UTC  
> Updated at: 2022-04-23 16:43:58 UTC  
> Closed at: 2022-04-23 16:43:58 UTC  
> Url: https://github.com/boostorg/property_map/issues/20  

Since  boost 1.74 building anything with `property_map.hpp` throws warning because of deprecated iterator header used.  
  
See: [https://www.boost.org/doc/libs/1_75_0/boost/detail/iterator.hpp](https://www.boost.org/doc/libs/1_75_0/boost/detail/iterator.hpp)  
Included here: [https://github.com/boostorg/property_map/blob/develop/include/boost/property_map/property_map.hpp](https://github.com/boostorg/property_map/blob/develop/include/boost/property_map/property_map.hpp)

---

## Comment 1

> Username: Lastique  
> Created at: 2021-02-25 16:36:57 UTC  
> Url: https://github.com/boostorg/property_map/issues/20#issuecomment-786035529  

@mclow This issue belongs to [Boost.PropertyMap](https://github.com/boostorg/property_map), not Boost.PropertyTree. And it's been fixed there in https://github.com/boostorg/property_map/commit/a1510e7e0fb6914c49109ceee1b2c5a83dc84428.

---

## Comment 2

> Username: mclow  
> Created at: 2021-02-25 17:34:16 UTC  
> Url: https://github.com/boostorg/property_map/issues/20#issuecomment-786075463  

Thanks. Transferred

---

## Comment 3

> Username: jeking3  
> Created at: 2022-04-23 16:43:58 UTC  
> Url: https://github.com/boostorg/property_map/issues/20#issuecomment-1107532304  

Fixed in 1.79.0
