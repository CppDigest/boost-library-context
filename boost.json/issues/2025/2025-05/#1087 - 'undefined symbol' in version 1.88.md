# #1087 - 'undefined symbol' in version 1.88 [Closed]

> Username: octopus-prime  
> Created at: 2025-05-31 07:49:07 UTC  
> Updated at: 2025-05-31 11:57:48 UTC  
> Closed at: 2025-05-31 11:57:47 UTC  
> Url: https://github.com/boostorg/json/issues/1087  

I was able to install boost 1.88 on windows - at least i think so.  
'Boost consulting' stopped providing windows installers so I needed to install via 'vcpkg'.  
  
I can build/run a complete rest-client including beast, json and url from boost.  
  
But if i want to build the Release version and code uses `json::value_from` the linker complains  
```  
lld-link : error : undefined symbol: __declspec(dllimport) private: static class boost::json::detail::default_resource boost::json::detail::default_resource::instance_   
```  
Debug with `json::value_from` builds fine. Release without `json::value_from`  builds fine.  
  
Example code  
https://github.com/octopus-prime/boost-rest-demo/blob/main/src/json/broken.cpp  
```  
#include <boost/json.hpp>  
#include <boost/describe.hpp>  
  
struct test {  
    int a;  
    int b;  
};  
  
BOOST_DESCRIBE_STRUCT(test, (), (a, b))  
  
int main() {  
    namespace json = boost::json;  
    json::value_from(test{});  
}  
```  
  
https://github.com/octopus-prime/boost-rest-demo/blob/main/CMakeLists.txt  
  
I am not sure whether the lib has a problem or vcpkg install is broken.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-05-31 08:32:02 UTC  
> Url: https://github.com/boostorg/json/issues/1087#issuecomment-2924683619  

I don't believe Boost Consulting has been a thing for quite a while. The official list of Boost release downloads is here: https://www.boost.org/releases/latest/.  
  
Given that Debug build works and Release build doesn't, I suspect that the boost_json binary you installed is incompatible with your Release build. But first please check that you do link to boost_json in Release build. If you do, install the release version of Boost.

---

## Comment 2

> Username: octopus-prime  
> Created at: 2025-05-31 11:57:47 UTC  
> Url: https://github.com/boostorg/json/issues/1087#issuecomment-2925063606  

Just changed toolchain from clang to msvc. Works fine now. Thanks :-)
