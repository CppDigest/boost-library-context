# #252 - Wrong return type of hana::traits::extent [Closed]

> Username: snizovtsev  
> Created at: 2016-02-19 15:28:26 UTC  
> Updated at: 2016-02-20 01:02:14 UTC  
> Closed at: 2016-02-20 01:02:14 UTC  
> Url: https://github.com/boostorg/hana/issues/252  

Hana's extent wrapper returns integral_constant of "const size_t" type instead of "size_t" as other traits do.  
  
So, this code  
  
``` cpp  
std::cout << boost::core::demangle(typeid(  
    hana::traits::extent(hana::type_c<int[2]>)  
).name()) << std::endl;  
```  
  
outputs  
  
```  
boost::hana::integral_constant<unsigned long const, 2ul>  
```  
  
This results to unexpected error when doing something like  
  
``` cpp  
hana::product<hana::integral_constant_tag<size_t>>(extents)  
```  
  
We need to specify Ring type here to handle empty extents but it doesn't compiles because "size_t" and "const size_t" treated as different Rings.
