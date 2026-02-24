# #185 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:45:59 UTC  
> Updated at: 2019-12-16 14:45:20 UTC  
> Closed at: 2019-12-16 14:45:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/185  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/archive/detail/basic_oarchive.hpp:        vsave(NULL_POINTER_TAG);  
./boost/archive/basic_archive.hpp:#define NULL_POINTER_TAG boost::archive::class_id_type(-1)  
./boost/archive/detail/iserializer.hpp:    #define DONT_USE_HAS_NEW_OPERATOR 1  
./boost/archive/detail/iserializer.hpp:    #define DONT_USE_HAS_NEW_OPERATOR 0  
./boost/archive/detail/iserializer.hpp:#if ! DONT_USE_HAS_NEW_OPERATOR  
./boost/archive/detail/iserializer.hpp:    #if DONT_USE_HAS_NEW_OPERATOR  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2019-12-16 14:45:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/185#issuecomment-566091255  

Fixed this, ran some local tests, committed and pushed to develop.   We'll leave it there until tests cycle.
