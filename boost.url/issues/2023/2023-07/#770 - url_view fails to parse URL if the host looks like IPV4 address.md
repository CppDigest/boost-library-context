# #770 - url_view fails to parse URL if the host looks like IPV4 address [Closed]

> Username: psrj0737  
> Created at: 2023-07-19 12:12:43 UTC  
> Updated at: 2023-07-19 12:21:21 UTC  
> Closed at: 2023-07-19 12:21:21 UTC  
> Url: https://github.com/boostorg/url/issues/770  

Hi, I think there's an issue when `boost::url_view` tries to parse a URL when subdomains contain an IP address.  
  
Here's a minimal example:  
  
```  
#include <boost/url/src.hpp>  
int main() {  
    boost::url_view a{"https://1.2.3.com/r"}; // ok  
    boost::url_view b{"https://1.2.3.256.com/r"}; // ok  
    boost::url_view c{"https://a.1.2.3.4.com/r"}; // ok  
    boost::url_view d{"https://1.2.3.4.com/r"}; // exception, what():  leftover [boost.url.grammar:4]  
    return 0;  
}  
```  
  
https://godbolt.org/z/6rhbdMcfY

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-07-19 12:21:21 UTC  
> Url: https://github.com/boostorg/url/issues/770#issuecomment-1641984487  

I just checked in develop and it works fine. It has been fixed in 3a237c35.
