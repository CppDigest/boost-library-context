# #207 - Compile error introduced in develop branch [Closed]

> Username: HDembinski  
> Created at: 2020-06-24 16:36:55 UTC  
> Updated at: 2020-07-15 23:02:53 UTC  
> Closed at: 2020-07-15 23:02:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/207  

The Boost Histogram tests that use Boost Serialization are failing with this error:  
```bash  
In file included from ../../boost/histogram/serialization.hpp:11:  
../../boost/serialization/map.hpp:45:33: error: no type named 'library_version_type' in namespace 'boost::serialization'; did you mean 'item_version_type'?  
    const boost::serialization::library_version_type library_version(  
          ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
                                item_version_type  
../../boost/serialization/item_version_type.hpp:25:7: note: 'item_version_type' declared here  
class item_version_type {  
      ^  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-07-15 23:02:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/207#issuecomment-659058400  

I think this has been fixed.  It's its not, feel free to re-open
