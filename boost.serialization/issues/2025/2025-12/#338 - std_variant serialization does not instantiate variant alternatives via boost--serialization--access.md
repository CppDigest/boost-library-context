# #338 - std_variant serialization does not instantiate variant alternatives via boost::serialization::access [Open]

> Username: KJTsanaktsidis  
> Created at: 2025-12-26 02:32:37 UTC  
> Updated at: 2025-12-27 00:25:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/338  

I have a variant that has some alternatives which do not have public default constructors. They _do_ have private default constructors though, and are friended to `boost::serialization::access`. However, the variant serializer in `boost/serialization/std_variant.hpp` can't construct these types.  
  
I wonder if the variant serializaer should, instead of this:  
  
https://github.com/boostorg/serialization/blob/8a8c62864f05732131bf6785d54466d8ac6cd477/include/boost/serialization/std_variant.hpp#L125  
  
Do something along the lines of  
  
```  
alignas(type) unsigned char value_storage[sizeof(type)];  
type *value_ptr = reinterpret_cast<type *>(value_storage);  
access::construct(value_ptr);  
std::experimental::scope_exit cleanup([&]() { access::destroy(value_ptr) };  
  
ar >> BOOST_SERIALIZATION_NVP(*value_ptr);  
v = std::move(*value_ptr);  
```  
  
If this sounds right I can send a PR. thoughts?

---

## Comment 1

> Username: KJTsanaktsidis  
> Created at: 2025-12-27 00:25:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/338#issuecomment-3693513293  

I opened a PR for this - https://github.com/boostorg/serialization/pull/339
