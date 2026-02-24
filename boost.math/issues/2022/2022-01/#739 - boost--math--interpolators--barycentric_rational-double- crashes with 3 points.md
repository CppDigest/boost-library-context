# #739 - boost::math::interpolators::barycentric_rational<double> crashes with 3 points [Closed]

> Username: thornton  
> Created at: 2022-01-11 22:54:49 UTC  
> Updated at: 2022-01-15 00:51:28 UTC  
> Closed at: 2022-01-15 00:51:28 UTC  
> Url: https://github.com/boostorg/math/issues/739  

boost::math::interpolators::barycentric_rational<double> works for 4 or more points but not for 2 or 3 points.  
Below code works with ns=4 but crashes for ns=3 .  
  
```cpp  
void main()  
{  
	std::vector<double> r(4);  
	std::vector<double> mrV(4);  
  
	r[0] = 1;  
	r[1] = 2;  
	r[2] = 3;  
	r[3] = 4;  
  
	mrV[0] = 0.1;  
	mrV[1] = 0.2;  
	mrV[2] = 0.3;  
	mrV[3] = 0.4;  
  
	int ns = 3;  
	boost::math::interpolators::barycentric_rational<double> b(r.data(), mrV.data(), ns);  
  
	for (size_t i = 1; i < 4; ++i)  
	{  
		double r = i * 0.5;  
		std::cout << "(r, V) = (" << r << ", " << -b(r) << ")\n";  
	}  
}  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-01-12 05:40:16 UTC  
> Updated at: 2022-01-12 05:43:05 UTC  
> Url: https://github.com/boostorg/math/issues/739#issuecomment-1010656624  

@thornton: Have you turned off exceptions? I get the following when I run your code:  
  
```  
$ ./main.x  
terminate called after throwing an instance of 'std::domain_error'  
  what():  Approximation order must be < data length.  
fish: Job 3, './main.x' terminated by signal SIGABRT (Abort)  
```  
  
If you want 3 points to work, you have to turn the approximation order lower:  
  
  
```cpp  
#include <iostream>  
#include <boost/math/interpolators/barycentric_rational.hpp>  
  
using boost::math::interpolators::barycentric_rational;  
  
int main()  
{  
    std::vector<double> r(4);  
    std::vector<double> mrV(4);  
    r[0] = 1;  
    r[1] = 2;  
    r[2] = 3;  
    r[3] = 4;  
  
    mrV[0] = 0.1;  
    mrV[1] = 0.2;  
    mrV[2] = 0.3;  
    mrV[3] = 0.4;  
    int ns = 3;  
    int approximation_order = 2;  
    barycentric_rational<double> b(r.data(), mrV.data(), ns, approximation_order);  
  
    for (size_t i = 1; i < 4; ++i)  
    {  
        double r = i * 0.5;  
        std::cout << "(r, V) = (" << r << ", " << -b(r) << ")\n";  
    }  
}  
```

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-01-15 00:51:28 UTC  
> Url: https://github.com/boostorg/math/issues/739#issuecomment-1013564715  

I believe the error message is currently sufficient. Please reopen if you feel otherwise.
