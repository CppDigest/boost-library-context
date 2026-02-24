# #979 - complex pow fails with boost::float128_t [Open]

> Username: gpeterhoff  
> Created at: 2023-04-26 13:02:20 UTC  
> Updated at: 2023-04-26 13:32:49 UTC  
> Url: https://github.com/boostorg/math/issues/979  

Hi,  
`std::pow(std::complex<boost::float128_t>, boost::float128_t>)` works not with negative real and zero imag, eg:  
```  
template <typename Type>  
void show_complex_pow(const Type r, const Type i, const Type e)  
{  
 std::complex<Type> c(r, i);  
 c = std::pow(c, e);  
 std::cout << c << std::endl;  
}  
const double  
 r = -0.7,  
 i = 0.0,  
 e = 2.3;  
show_complex_pow<double>(r, i, e);  
show_complex_pow<boost::float128_t>(r, i, e);  
```  
(+0.258788,+0.356191)  
(-nan,+0)

---

## Comment 1

> Username: mborland  
> Created at: 2023-04-26 13:13:01 UTC  
> Url: https://github.com/boostorg/math/issues/979#issuecomment-1523400456  

Are you using GCC on x86-64 so `boost::float128_t` is a typedef of `__float128`? Does it work if you call `cpowq` instead of `pow`?

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-04-26 13:32:49 UTC  
> Url: https://github.com/boostorg/math/issues/979#issuecomment-1523431737  

yes + yes  
```  
__complex128 c = r + i;  
 c= cpowq(c, e);  
 std::cout << '(' <<crealq(c) << ','<< cimagq(c) << ")\n";  
```  
(+0.258788,+0.356191)  
The problem is the overload in boost/math/cstdfloat/cstdfloat_complex_std.hpp
