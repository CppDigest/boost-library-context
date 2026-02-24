# #326 - GCC 14: warning about not using  'boost::archive::class_id_type::operator base_type&()' [Open]

> Username: smuzaffar  
> Created at: 2025-02-22 10:23:17 UTC  
> Updated at: 2025-02-24 14:47:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/326  

Hi,  
  We have a generic load routine for all types that feature conversion operators. Compiling this code with GCC 14 warns that compiler is not using the conversion operators [a]. Was there any specific reason to make `base_type` of these classes (which provide conversion operator[b]) private? If `base_type` is public then we could do something like  
```  
load(static_cast<T::base_type &>(t));  
```  
[a]  
```  
portable_iarchive.hpp#L412:12: warning: casting 'boost::archive::class_id_type' to 'boost::uint_t<16>::least&' {aka 'short unsigned int&'} does not use 'boost::archive::class_id_type::operator base_type&()' [-Wcast-user-defined]  
   412 |       load((typename boost::uint_t<sizeof(T) * CHAR_BIT>::least&)(t));  
```  
  
[b]  
- https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_archive.hpp#L44  
- https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_archive.hpp#L77  
- https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_archive.hpp#L116  
- and few classes in https://github.com/boostorg/serialization/tree/develop/include/boost/serialization
