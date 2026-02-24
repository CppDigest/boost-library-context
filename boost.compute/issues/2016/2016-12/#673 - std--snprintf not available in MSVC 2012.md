# #673 - std::snprintf not available in MSVC 2012 [Closed]

> Username: Ravi0li  
> Created at: 2016-12-05 10:27:56 UTC  
> Updated at: 2017-03-21 03:11:49 UTC  
> Closed at: 2017-03-21 03:11:47 UTC  
> Url: https://github.com/boostorg/compute/issues/673  

With MSVC 2012 I have problems in ```paramter_cach.hpp``` with the function ```std::snprintf```. The problem may also appear on MSVC 2013. This is my workaround:  
```  
#ifdef BOOST_COMPUTE_USE_OFFLINE_CACHE  
#include <boost/algorithm/string/trim.hpp>  
#include <boost/compute/detail/path.hpp>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
#if defined (_MSC_VER) && _MSC_VER < 1900  
#define SNPRINTF _snprintf  
#else  
#define SNPRINTF std::snprintf  
#endif  
#endif // BOOST_COMPUTE_USE_OFFLINE_CACHE  
```  
```  
    // returns the boost.compute version string  
    static std::string version_string()  
    {  
        char buf[32];  
        SNPRINTF(buf, sizeof(buf), "%d.%d.%d", BOOST_COMPUTE_VERSION_MAJOR,  
                                                    BOOST_COMPUTE_VERSION_MINOR,  
                                                    BOOST_COMPUTE_VERSION_PATCH);  
          
        return buf;  
    }  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2017-03-21 03:11:46 UTC  
> Url: https://github.com/boostorg/compute/issues/673#issuecomment-287964350  

Should be fixed by #694.
