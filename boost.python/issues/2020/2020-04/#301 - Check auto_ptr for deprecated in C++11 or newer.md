# #301 - Check auto_ptr for deprecated in C++11 or newer [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-04 16:47:54 UTC  
> Updated at: 2020-04-11 17:16:30 UTC  
> Closed at: 2020-04-11 17:16:30 UTC  
> Url: https://github.com/boostorg/python/issues/301  

GCC 7.5 fires deprecated-declarations warning in Boost 1.72.  
  
include/boost/python/detail/is_auto_ptr.hpp:17:40: error: ‘template<class> class std::auto_ptr’ is deprecated [-Werror=deprecated-declarations]

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-11 17:16:30 UTC  
> Url: https://github.com/boostorg/python/issues/301#issuecomment-612468869  

Boost should be built with `-DBOOST_NO_AUTO_PTR` to avoid `std::auto_ptr` GCC -Wdeprecated-declarations warnings.
