# #583 - std::pmr::memory_resource incompatible with boost::container::pmr::memory_resource [Open]

> Username: accelerated  
> Created at: 2021-06-18 15:39:58 UTC  
> Updated at: 2021-06-18 22:24:09 UTC  
> Url: https://github.com/boostorg/json/issues/583  

### Version of Boost  
1.75  
  
### Steps necessary to reproduce the problem  
Seems that any stl pmr resources are incompatible with the boost ones. This is quite undesirable as now I have to use two memory resources when dealing with json types.   
```c++  
boost::json::storage_ptr{std::pmr::monotonic_buffer_resource()}; //does not compile  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-18 15:42:15 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864126212  

Does Boost.JSON's standalone version meet your needs? It uses `std::pmr::memory_resource`:  
https://github.com/boostorg/json#without-boost

---

## Comment 2

> Username: accelerated  
> Created at: 2021-06-18 15:50:42 UTC  
> Updated at: 2021-06-18 15:55:14 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864130897  

We are using other boost libraries in our system so we prefer to use the non-standalone version. Isn't it possible to support both boost and stl resources?

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-06-18 16:13:57 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864144488  

What is the compilation error?

---

## Comment 4

> Username: accelerated  
> Created at: 2021-06-18 19:05:19 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864224227  

I want to use this constructor:  
```c++  
template<class T  
#ifndef BOOST_JSON_DOCS  
        , class = typename std::enable_if<  
            std::is_convertible<T*,  
                memory_resource*>::value>::type  
#endif  
    >  
    storage_ptr(T* r) noexcept  
        : i_(reinterpret_cast<std::uintptr_t>(  
                static_cast<memory_resource *>(r)) +  
            (json::is_deallocate_trivial<T>::value ? 2 : 0))  
    {  
        BOOST_ASSERT(r);  
    }  
```  
The error says `no type named ‘type’ in ‘struct std::enable_if<false, void>’` which indicates the `is_convertible` failure. Then compiler tries all other constructors and none match.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-06-18 19:11:39 UTC  
> Updated at: 2021-06-18 19:12:26 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864226913  

>  Isn't it possible to support both boost and stl resources?  
  
There would be a performance cost. In this case we would have to allocate memory upon construction to store a small adaptor which translates calls to Boost's memory resource into std's memory resource. Can you use the buffer resources that come with Boost.JSON instead? E.g.:  
  
https://github.com/boostorg/json/blob/351603c9b2dee7a80c8433841b50f7294131b25a/include/boost/json/monotonic_resource.hpp#L86

---

## Comment 6

> Username: accelerated  
> Created at: 2021-06-18 19:34:41 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864236390  

@vinniefalco Yes that's what I'm doing i.e. using the boost version but only for the json part. However I have other types which I serialize/deserialize to json and these types use the stl counterpart. I don't want to use the boost allocators because 1) the json conversion is fairly localized 2) don't want to pollute our vacabulary with boost types if it's not necessary.   
In terms of the performance cost, you could make this adaptor on the stack e.g. a private member of the storage_ptr which in itself only contains one other ptr (the stl pmr resource) like a PIMPL paradigm. So the penalty would be at most 8 bytes and the additional ptr dispatch which would likely be optimized away by the compiler (non  virtual)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-06-18 22:21:40 UTC  
> Updated at: 2021-06-18 22:24:09 UTC  
> Url: https://github.com/boostorg/json/issues/583#issuecomment-864300845  

> In terms of the performance cost, you could make this adaptor on the stack e.g. a private member of the storage_ptr which in itself only contains one other ptr  
  
The library performance is extremely sensitive to the sizes of the various types, i.e. `sizeof(storage_ptr)`. If we make this type bigger, then it will make everything else bigger including `value` and therefore `array`, `string`, and `object`. This will reduce the parsing and container operation performance.  
  
We could allow `std::pmr` resources to be specified as the type in calls to `make_shared_storage`, and hide the adaptor in there, which as you said would not be particular burdensome for the additional function dispatch. However then the resource would be counted, which does cost one allocation for the initial `storage_ptr`. It would look like this:  
  
```  
json::storage_ptr sp =  
    json::make_shared_storage<std::pmr::monotonic_buffer_resource>();  
  
json::value v( sp ); // uses the new monotonic_buffer_resource  
```  
  
Another possibility is to add the adaptor as a public type which you have to explicitly create, like this:  
```  
std::pmr::monotonic_buffer_resource mbr;  
auto mr =json::make_resource_adaptor( &mbr );  
  
json::value v( &mr ); // uses the existing monotonic_buffer_resource  
```  
  
The type of `mr` would be `json::memory_resource_adaptor<std::pmr::monotonic_buffer_resource>`.
