# #166 - add nameof into boost::core::demangle.hpp [Open]

> Username: gpeterhoff  
> Created at: 2024-02-07 19:35:53 UTC  
> Updated at: 2024-02-07 20:01:27 UTC  
> Url: https://github.com/boostorg/core/issues/166  

```  
#include <typeinfo>  
  
namespace boost  
{  
namespace core  
{  
template <typename Type>  
inline std::string	nameof()  
{  
	return demangle(typeid(Type).name());  
}  
  
}	//	core  
}	//	boost  
```  
This saves a lot of manual fiddling around.  
  
thx  
Gero

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-07 19:42:19 UTC  
> Url: https://github.com/boostorg/core/issues/166#issuecomment-1932747078  

See `boost::core::type_name`.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-07 20:01:26 UTC  
> Url: https://github.com/boostorg/core/issues/166#issuecomment-1932773747  

Thank you. I overlooked that.  
  
regards  
Gero
