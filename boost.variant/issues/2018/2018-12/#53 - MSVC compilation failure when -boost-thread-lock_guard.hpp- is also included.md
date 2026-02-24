# #53 - MSVC compilation failure when <boost/thread/lock_guard.hpp> is also included [Closed]

> Username: vldt  
> Created at: 2018-12-06 23:55:34 UTC  
> Updated at: 2020-07-23 12:28:15 UTC  
> Closed at: 2020-07-23 12:28:15 UTC  
> Url: https://github.com/boostorg/variant/issues/53  

The following code does not compile on MSVC 15.5.6 and 15.9.3 but compiles correctly in Clang 7.0.0 and GCC 8.2.  
  
Tested using Boost 1.68.0.  
  
This is triggered by the use of boost::variant::apply_visitor with an r-value ref to a variant when <boost/thread/lock_guard.hpp> is also included.  
  
Godbolt here: https://godbolt.org/z/uTH727  
  
Code:  
```  
#include <boost/variant.hpp>  
#include <boost/thread/lock_guard.hpp> // remove this to compile on MSVC  
  
struct spanac{  
};  
  
struct ceapa{  
    double a,b;  
};  
  
  
using var_t = boost::variant<spanac, ceapa>;  
  
struct visitor_t : public boost::static_visitor<bool>  
{  
    bool operator() (const spanac&) const  
    {  
        return true;  
    }  
  
    bool operator() (const ceapa&) const  
    {  
        return false;  
    }  
private:  
double a,b;  
};  
  
var_t get(){  
    volatile int k = 0;  
  
    if (k)  
        return spanac();  
    else  
        return ceapa();  
}  
  
int main()  
{  
    visitor_t v;  
    bool result = boost::apply_visitor(v, get());  
  
    return 0;  
}  
```  
  
A valid workaround would be to pass an l-value reference variant to apply_visitor:   
```  
...  
    visitor_t v;  
    auto var = get();  
    bool result = boost::apply_visitor(v, var);  
...  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-08 08:10:18 UTC  
> Url: https://github.com/boostorg/variant/issues/53#issuecomment-452209627  

Repeated the bug and reported it into the Boost.Thread https://github.com/boostorg/thread/issues/264  
  
Many thanks for the report and for the reproducable example!
