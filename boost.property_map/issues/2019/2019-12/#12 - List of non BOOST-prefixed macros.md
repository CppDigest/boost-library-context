# #12 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:59:51 UTC  
> Updated at: 2019-12-25 00:53:37 UTC  
> Closed at: 2019-12-25 00:53:37 UTC  
> Url: https://github.com/boostorg/property_map/issues/12  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/property_map/dynamic_property_map.hpp:#ifndef DYNAMIC_PROPERTY_MAP_RG09302004_HPP  
./boost/property_map/dynamic_property_map.hpp:#define DYNAMIC_PROPERTY_MAP_RG09302004_HPP  
./boost/property_map/dynamic_property_map.hpp:#endif // DYNAMIC_PROPERTY_MAP_RG09302004_HPP  
./boost/property_map/vector_property_map.hpp:#ifndef VECTOR_PROPERTY_MAP_HPP_VP_2003_03_04  
./boost/property_map/vector_property_map.hpp:#define VECTOR_PROPERTY_MAP_HPP_VP_2003_03_04  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-25 00:53:37 UTC  
> Url: https://github.com/boostorg/property_map/issues/12#issuecomment-568815408  

Fixed in ab086d43e09a221f65f9a701bb8fb02f105ebfce.
