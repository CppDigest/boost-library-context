# #1308 - Turning off double promotion can result in negative values for noncentral T CDF [Closed]

> Username: dschmitz89  
> Created at: 2025-08-23 20:24:19 UTC  
> Updated at: 2025-08-26 10:19:44 UTC  
> Closed at: 2025-08-26 10:19:44 UTC  
> Url: https://github.com/boostorg/math/issues/1308  

Over at SciPy, I ran into an issue where the policy we apply dramatically worsens the result of the noncentral T distribution CDF. The default policy gives the correct result (confirmed against multiprecision library) while turning off double promotion results in a negative value . Reproducer:  
  
Machine: Linux (WSL)/x86_64  
Compiler: g++ 14.3  
   
<details><summary>Details</summary>  
<p>  
  
```c++  
#include <iostream>  
#include <iomanip>  
#include <cstdlib>  
#include <boost/math/distributions/non_central_t.hpp>  
  
typedef boost::math::policies::policy<  
    boost::math::policies::promote_double<false>  
> SciPyPolicy;  
  
int main(int argc, char* argv[])  
{  
    if (argc != 4) {  
        std::cerr << "Usage: " << argv[0] << " v l x\n";  
        return 1;  
    }  
  
    double v = std::atof(argv[1]);  
    double l = std::atof(argv[2]);  
    double x = std::atof(argv[3]);  
  
    // With policy  
    double y_policy = boost::math::cdf(  
        boost::math::non_central_t_distribution<double, SciPyPolicy>(v, l), x);  
  
    // Without policy (uses default policy)  
    double y_default = boost::math::cdf(  
        boost::math::non_central_t_distribution<double>(v, l), x);  
  
    std::cout << std::setprecision(17);  
    std::cout << "With SciPyPolicy:    cdf(noncentral_t; x=" << x << ", v=" << v << ", l=" << l << ") = " << y_policy << std::endl;  
    std::cout << "With default policy: cdf(noncentral_t; x=" << x << ", v=" << v << ", l=" << l << ") = " << y_default << std::endl;  
  
    return 0;  
}  
```   
  
</p>  
</details>   
  
  
Compile with `g++ -std=c++17 -I ..../boost_math/math/include -o noncentral_t_reppro noncentral_t_reproducer.cpp`.  
  
Invoke `./noncentral_t_reppro 980. 38. 1.5`  
  
Output:  
```  
With SciPyPolicy:    cdf(noncentral_t; x=1.5, v=980, l=38) = -1.505990970127819e-70  
With default policy: cdf(noncentral_t; x=1.5, v=980, l=38) = 1.1824454111413493e-291  
```   
  
A probability should not be outside [0, 1]. Is this some thing that can be fixed on our end or in the code itself?  
  
Another case where the default policy gives a more accurate result:  
  
```  
./noncentral_t_reppro 3. 5. -2.  
With SciPyPolicy:    cdf(noncentral_t; x=-2, v=3, l=5) = 1.5645374205544151e-09  
With default policy: cdf(noncentral_t; x=-2, v=3, l=5) = 1.5645373999137148e-09  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-08-24 08:39:39 UTC  
> Url: https://github.com/boostorg/math/issues/1308#issuecomment-3217943384  

Confirmed, this is a side effect of a previous fix pushing the non-central beta series into the unstable direction in extreme cases, it needs a check in the series that we're not iterating over garbage.

---

## Comment 2

> Username: dschmitz89  
> Created at: 2025-08-26 09:38:21 UTC  
> Url: https://github.com/boostorg/math/issues/1308#issuecomment-3223419248  

Thanks for the quick fix! I guess this can be closed then?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-08-26 10:19:44 UTC  
> Url: https://github.com/boostorg/math/issues/1308#issuecomment-3223553700  

Yes.  Actually I thought the PR would close this...
