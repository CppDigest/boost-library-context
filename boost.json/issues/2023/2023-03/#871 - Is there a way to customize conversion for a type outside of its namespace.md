# #871 - Is there a way to customize conversion for a type outside of its namespace? [Closed]

> Username: Yosh31207  
> Created at: 2023-03-10 14:51:52 UTC  
> Updated at: 2023-06-02 04:28:42 UTC  
> Closed at: 2023-06-02 04:28:42 UTC  
> Url: https://github.com/boostorg/json/issues/871  

### Version of Boost  
  
1.81  
  
### Question  
  
I want to make `value_to` and `value_from` conversions for a type `T` defined in third-party library. It works properly if `tag_invoke` function is defined in `boost::json` namespace or the same namespace as `T`, but it causes compile error if the function is defined in other namespaces.  
  
What I would like to do is as the follows.  
  
```C++  
// third_party.hpp  
  
namespace third_party {  
    class TheirType;  
}  
```  
  
```C++  
// my_app.cpp  
  
#include "third_party.hpp"  
#include <boost/json.hpp>  
  
using third_party::TheirType;  
  
namespace {    
    TheirType tag_invoke(const boost::json::value_to_tag<TheirType>, const boost::json::value& json) {  
        /* implementation*/  
    }  
  
    void tag_invoke(const boost::json::value_from_tag&, boost::json::value& json, const TheirType& theirs) {  
        /* implementation*/  
    }  
}  
  
namespace my_app {  
    void my_func(const boost::json::value& json) {  
        auto t = boost::json::value_to<TheirType>(json);  
    }  
}  
```  
  
If possible, I want to define `tag_invoke` overload function in unnamed namespace or `my_app` namespace not to add changes for `third_party` and `boost::json` namespaces. Is there a way to do it?  
  
Let me add that it is not very important because I can work around it by defining it in `boost::json` namespace.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-03-10 15:24:17 UTC  
> Url: https://github.com/boostorg/json/issues/871#issuecomment-1463962041  

Not currently, but I implemented the necessary change in #819. Check out the docs https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/json/conversion/contextual_conversions.html.  
  
If everything is good, it will be released in 1.83
