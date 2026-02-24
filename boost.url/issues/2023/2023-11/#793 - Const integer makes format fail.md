# #793 - Const integer makes format fail [Closed]

> Username: LarsGullik  
> Created at: 2023-11-27 15:43:51 UTC  
> Updated at: 2023-11-27 19:53:18 UTC  
> Closed at: 2023-11-27 19:53:18 UTC  
> Url: https://github.com/boostorg/url/issues/793  

If a const integer is passed to format then compilation fails.  
  
```  
#include <boost/url/format.hpp>  
  
struct HostPort {  
    const std::string_view host{"a.host"};  
    //const unsigned short int port{80}; // does not compile  
    unsigned short int port{80}; // compiles  
};  
  
int main()  
{  
    HostPort hp;  
    auto url = boost::urls::format("http://{}:{}", hp.host, hp.port);  
}  
```  
  
Ref. https://godbolt.org/z/EdrY9cvnG  
  
There seems to be some missing `std::remove_cv` somewhere.
