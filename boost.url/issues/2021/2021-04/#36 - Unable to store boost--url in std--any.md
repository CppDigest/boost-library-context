# #36 - Unable to store boost::url in std::any [Closed]

> Username: ghost  
> Created at: 2021-04-08 08:37:45 UTC  
> Updated at: 2021-09-30 15:03:34 UTC  
> Closed at: 2021-09-30 15:03:34 UTC  
> Url: https://github.com/boostorg/url/issues/36  

I am trying to store a boost::url object inside of an std::any, but receive compilation errors regarding the copy constructor of boost::basic_url, which does not appear to be calling into the boost::url_base constructor properly (I may be interpreting the compiler warnings poorly).  
  
The compilation errors can be seen via the minimal C++ program:  
  
```c++  
#include <boost/url.hpp>  
#include <iostream>  
#include <any>  
  
static constexpr auto github_url = "https://github.com/CPPAlliance/url";  
  
int main()  
{  
    std::any anything;  
    anything.emplace<boost::url>(github_url);  
    std::cout << std::any_cast<boost::url&>(anything).encoded_url() << std::endl;  
}  
```

---

## Comment 1

> Username: AlexandreBossard  
> Created at: 2021-05-25 09:24:23 UTC  
> Url: https://github.com/boostorg/url/issues/36#issuecomment-847708165  

I've ran into this myself. The copy/move operators and constructor of `url_base` seems to be the culprit. Anything that require to copy/move a `boost::url` fails.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-05-25 19:30:57 UTC  
> Url: https://github.com/boostorg/url/issues/36#issuecomment-848203686  

Note that this library is still in development

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-09-14 04:53:46 UTC  
> Url: https://github.com/boostorg/url/issues/36#issuecomment-918800449  

`url` is rewritten to do away with the base, and I've added `static_url`. Copy and move should work, please try it!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-30 15:03:34 UTC  
> Url: https://github.com/boostorg/url/issues/36#issuecomment-931405454  

copy/move pass tests now
