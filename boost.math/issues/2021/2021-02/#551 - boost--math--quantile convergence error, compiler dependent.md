# #551 - boost::math::quantile convergence error, compiler dependent [Closed]

> Username: a-ma72  
> Created at: 2021-02-22 07:59:40 UTC  
> Updated at: 2021-02-23 10:12:13 UTC  
> Closed at: 2021-02-23 10:12:13 UTC  
> Url: https://github.com/boostorg/math/issues/551  

I get convergence errors on every attempt to calculate the quantile of the non-central t-distribution, when compiling with c++ (tdm64-1) 9.2.0 (win10, 64bit), only:  
```  
Error in function quantile(const non_central_t_distribution<long double>, long double): Unable to locate solution in a reasonable time: either  
there is no answer to quantile or the answer is infinite.  Current best guess is 2.80036948820444298036  
```  
  
```  
#define BOOST_MATH_MAX_ROOT_ITERATION_POLICY 200  
#include <boost/math/distributions.hpp>  
#include <iostream>  
  
void test()  
{  
    boost::math::non_central_t nct( /*nu*/ 14, /*delta*/ 0.9 );  
    try  
    {  
        const double alpha = 0.05;  
        double value = boost::math::quantile( nct, 1.0 - alpha );  
    }  
    catch( std::exception const&  e )  
    {  
        std::cout << "Error message: " << e.what() << endl;  
    }  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-22 18:26:29 UTC  
> Url: https://github.com/boostorg/math/issues/551#issuecomment-783577293  

Works for me with msvc and gcc-mingw/msys.  
  
I'm not familiar with tdm, but I'll try and give it a whirl.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-22 18:39:31 UTC  
> Url: https://github.com/boostorg/math/issues/551#issuecomment-783585734  

Works for me with TDM 64-bit build as well.  What's your compiler command line?

---

## Comment 3

> Username: a-ma72  
> Created at: 2021-02-23 10:12:13 UTC  
> Url: https://github.com/boostorg/math/issues/551#issuecomment-784069752  

I compiled with boost version 1.73. Now, with update to version 1.75 all works fine.
