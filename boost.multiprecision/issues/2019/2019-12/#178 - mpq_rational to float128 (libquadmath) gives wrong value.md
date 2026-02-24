# #178 - mpq_rational to float128 (libquadmath) gives wrong value [Closed]

> Username: AuroraDysis  
> Created at: 2019-12-31 12:12:41 UTC  
> Updated at: 2020-03-13 11:37:16 UTC  
> Closed at: 2020-03-13 11:37:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/178  

```cpp  
mpq_rational test1(3, 4);  
std::cout << test1.convert_to<long double>() << std::endl;  
cpp_rational test2(3, 4);  
std::cout << test2.convert_to<long double>() << std::endl;  
```  
output  
```  
1.18973e+4932  
0.75  
```  
compiler `gcc (GCC) 8.3.1 20190507 (Red Hat 8.3.1-4)`  
gmp version `6.1.2`  
boost version `1.72.0`
