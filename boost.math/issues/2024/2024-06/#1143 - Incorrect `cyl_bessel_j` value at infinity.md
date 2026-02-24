# #1143 - Incorrect `cyl_bessel_j` value at infinity [Closed]

> Username: matwey  
> Created at: 2024-06-02 19:23:14 UTC  
> Updated at: 2024-06-14 17:34:25 UTC  
> Closed at: 2024-06-14 17:34:25 UTC  
> Url: https://github.com/boostorg/math/issues/1143  

Hello,  
  
I use the following code with boost 1.83.0  
```c++  
std::cout << boost::math::cyl_bessel_j(0, std::numeric_limits<double>::infinity()) << std::endl;  
std::cout << boost::math::cyl_bessel_j(1, std::numeric_limits<double>::infinity()) << std::endl;  
```  
and then I see  
```  
-nan  
-nan  
```  
while Bessel J function of any order is known to be exactly zero at infinity.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-06-03 12:16:08 UTC  
> Url: https://github.com/boostorg/math/issues/1143#issuecomment-2145045944  

Confirmed, I'll check the other Bessel functions while I'm at it.  Thanks for the report!

---

## Comment 2

> Username: matwey  
> Created at: 2024-06-03 13:20:32 UTC  
> Url: https://github.com/boostorg/math/issues/1143#issuecomment-2145188709  

Thanks. I've found that at least `sinc_pi` is also not defined correctly for infinity.
