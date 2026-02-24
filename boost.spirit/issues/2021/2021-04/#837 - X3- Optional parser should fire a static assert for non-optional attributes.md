# #837 - X3: Optional parser should fire a static assert for non-optional attributes [Open]

> Username: Kojoley  
> Created at: 2021-04-18 17:11:22 UTC  
> Updated at: 2025-10-05 10:15:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/837  

```cpp  
#include <boost/spirit/home/x3.hpp>  
int main()  
{  
    using boost::spirit::x3::int_;  
    char const* const it = "";  
    int i;  
    parse(it, it, -int_, i);  
}  
```  
  
The problem is that after a successful parsing the attribute will be uninitialized, so accessing it will invoke UB. It is pretty much the same story as boostorg/spirit#608. Currently we cannot reliably distinguish plain attributes from optional, and just assume it could be an optional-like UDT without any checking. The `is_optional` trait needs a refinement before it can be used for implementing the static assert.
