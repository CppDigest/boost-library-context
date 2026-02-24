# #508 - boost::hana::drop_front doesn't respect refness [Open]

> Username: KierenP  
> Created at: 2022-08-17 04:08:10 UTC  
> Updated at: 2022-08-17 04:23:15 UTC  
> Url: https://github.com/boostorg/hana/issues/508  

```  
#include <boost/hana/ext/std/tuple.hpp>  
#include <boost/hana/fold_left.hpp>  
#include <boost/hana/find_if.hpp>  
#include <boost/hana/for_each.hpp>  
#include <boost/hana/transform.hpp>  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/type.hpp>  
  
#include <iostream>  
#include <tuple>  
#include <vector>  
  
namespace hana = boost::hana;  
  
int main()  
{  
    int x = 1;  
    int y = 2;  
    int z = 3;  
  
    std::tuple<int&, int&, int&> refs {x, y, z};  
    auto ref_drop = boost::hana::drop_front(refs);  
  
    x = 10;  
    y = 20;  
    z = 30;  
  
    std::cout << x << " " << y << " " << z << std::endl;  
    std::cout << std::get<0>(refs) << " " << std::get<1>(refs) << " " << std::get<2>(refs) << std::endl;  
    std::cout << std::get<0>(ref_drop) << " " << std::get<1>(ref_drop) << std::endl;  
}  
```  
The output of the above code is:   
```  
10 20 30  
10 20 30  
2 3  
```  
Expected output is:  
```  
10 20 30  
10 20 30  
20 30  
```  
I would expect if I pass `boost::hana::drop_front` a `std::tuple<T&, U&, V&>` to get back a `std::tuple<U&, V&>` but instead I get a `std::tuple<U, V>`. This is unexpected. Is this intended behavior?

---

## Comment 1

> Username: KierenP  
> Created at: 2022-08-17 04:19:35 UTC  
> Updated at: 2022-08-17 04:23:15 UTC  
> Url: https://github.com/boostorg/hana/issues/508#issuecomment-1217443482  

As an aside, what is the best way to iterate over tuple elements until a condition is true? For example if I have a std::tuple (`std::tuple<X, Y, Z>`) and I want to call `.fn(...)` on each element I can do it like this:  
  
```  
    boost::hana::for_each(my_tuple, [](auto&& x)   
    {   
        x.fn();  
    });  
```  
  
But if I want to iterate only until a precondition is hit, I can't figure out how to do this  
```  
    auto first_ok_result = boost::hana::for_each(my_tuple, [](auto&& x)   
    {   
        auto result = x.fn();  
        if (result.status == status::ok)  
            return result.value; // I want to stop the for_each and return this value  
    });  
```
