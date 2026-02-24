# #16 fix ctti_type_index for GCC 7 [Merged]

> Username: bebuch  
> Created at: 2017-07-28 13:10:10 UTC  
> Updated at: 2017-07-29 17:14:56 UTC  
> Merged at: 2017-07-29 17:13:05 UTC  
> Closed at: 2017-07-29 17:13:05 UTC  
> Url: https://github.com/boostorg/type_index/pull/16  

`__PRETTY_FUNCTION__` is slightly different in GCC 7.  
  
The output of an unsigned int literal doesn't contain the 'u' anymore.  
  
```  
#include <iostream>  
  
template < typename T >  
struct ctti{  
    template < unsigned int D = 1 >  
    static constexpr auto name()noexcept{  
        return __PRETTY_FUNCTION__;  
    }  
};  
  
int main(){  
    using ctti = ctti< int >;  
    std::cout << ctti::name() << '\n';  
}  
```

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-07-29 17:14:56 UTC  
> Url: https://github.com/boostorg/type_index/pull/16#issuecomment-318845013  

Thanks for the awesome patch!  
  
Hopefully this fix will appear in Boost 1.65

---
