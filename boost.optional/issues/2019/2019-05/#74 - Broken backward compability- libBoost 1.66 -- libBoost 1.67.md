# #74 - Broken backward compability: libBoost 1.66 -> libBoost 1.67 [Open]

> Username: PiotrZSL  
> Created at: 2019-05-18 19:08:32 UTC  
> Updated at: 2019-05-24 08:45:31 UTC  
> Url: https://github.com/boostorg/optional/issues/74  

Problematic code:  
  
```  
#include <boost/optional/optional.hpp>  
  
struct A  
{  
    A(int a) : a(a) {}  
  
    int a;  
};  
  
 #define TRUE 1  
  
int main()  
{  
    return boost::optional<A>(TRUE , 5)->a;  
}  
  
```  
  
libBoost 1.66 calls here:  
optional ( bool cond, rval_reference_type val )  
boost::optional<A>(1, A(5));  
and returns 5,  
  
libBoost 1.67 and newer calls here:  
template<class Expr>  
explicit optional ( Expr&& expr,  
                        BOOST_DEDUCED_TYPENAME boost::enable_if< optional_detail::is_optional_val_init_candidate<T, Expr>, bool>::type = true  
)  
and returns 1.  
  
This is change of behavior without any warring.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-05-24 08:45:31 UTC  
> Url: https://github.com/boostorg/optional/issues/74#issuecomment-495526587  

Thanks for reporting this. I just want to let you know that I recorded this issue. I am too busy at the moment to address it.
