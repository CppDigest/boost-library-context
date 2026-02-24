# #894 - decltype(host()) conversion to std::string_view invalid [Closed]

> Username: mitsubishirgb  
> Created at: 2025-01-05 01:27:39 UTC  
> Updated at: 2025-01-06 14:49:02 UTC  
> Closed at: 2025-01-06 14:49:00 UTC  
> Url: https://github.com/boostorg/url/issues/894  

the key.host variable is corrupted data can someone tell me what do i do   
  
```  
#include <iostream>  
#include <boost/url.hpp>  
#include <string_view>  
#include <string>  
  
using boost::urls::url_view;  
  
  
struct ConKey {  
    boost::core::string_view host;  
};  
class Container : url_view{  
    Container(const char* url) : url_view(url) , key{host()}  
    {  
    }  
  
  
    ConKey key;  
};  
  
int main()  
{  
      
    Container cont("https://192.168.100.145:8080/home/");  
    std::cout << cont.key.host << std::endl;  
  
    return 0;  
}  
```

---

## Comment 1

> Username: mitsubishirgb  
> Created at: 2025-01-05 01:38:38 UTC  
> Url: https://github.com/boostorg/url/issues/894#issuecomment-2571464769  

oh i should have used the encoded_host() i guess

---

## Comment 2

> Username: alandefreitas  
> Created at: 2025-01-06 14:49:00 UTC  
> Url: https://github.com/boostorg/url/issues/894#issuecomment-2573267548  

`url_view::host()` returns a `std::string`, so you're storing a reference to a temporary string. You can either store a `std::string` or use `encoded_host()` *if* you're sure the underlying URL string will be alive for as long as your `Container` is.
