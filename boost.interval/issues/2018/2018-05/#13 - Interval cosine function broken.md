# #13 - Interval cosine function broken [Open]

> Username: NAThompson  
> Created at: 2018-05-08 02:38:23 UTC  
> Updated at: 2022-07-27 18:22:08 UTC  
> Url: https://github.com/boostorg/interval/issues/13  

Code to reproduce:  
  
```  
#include <boost/numeric/interval.hpp>  
  
using boost::numeric::interval;  
using namespace boost::numeric::interval_lib;  
  
int main()  
{  
    auto x = cos(interval<double>(2.3));  
    return 0;  
}  
```  
  
Error message:  
  
```  
g++-7 -Wall -Wextra -Wfatal-errors -fext-numeric-literals main.cpp -o main.x -pthread  
main.cpp: In function 'int main()':  
main.cpp:8:10: warning: variable 'x' set but not used [-Wunused-but-set-variable]  
     auto x = cos(interval<double>(2.3));  
          ^  
In file included from ../numeric/interval/include/boost/numeric/interval.hpp:30:0,  
                 from main.cpp:1:  
../numeric/interval/include/boost/numeric/interval/transc.hpp: In instantiation of 'boost::numeric::interval<T, Policies> boost::numeric::cos(const boost::numeric::interval<T, Policies>&) [with T = double; Policies = boost::numeric::interval_lib::policies<boost::numeric::interval_lib::rounded_math<double>, boost::numeric::interval_lib::checking_strict<double> >]':  
main.cpp:8:39:   required from here  
../numeric/interval/include/boost/numeric/interval/transc.hpp:73:18: error: 'boost::numeric::interval_lib::policies<boost::numeric::interval_lib::rounded_math<double>, boost::numeric::interval_lib::checking_strict<double> >::rounding {aka struct boost::numeric::interval_lib::rounded_math<double>}' has no member named 'cos_down'; did you mean 'conv_down'?  
     return I(rnd.cos_down(u), rnd.cos_up(l), true);  
              ~~~~^~~~~~~~  
              conv_down  
compilation terminated due to -Wfatal-errors.  
make: *** [main.x] Error 1  
```  
  
Compiled against a fresh clone.

---

## Comment 1

> Username: mclow  
> Created at: 2018-05-08 14:32:13 UTC  
> Url: https://github.com/boostorg/interval/issues/13#issuecomment-387422915  

I have reproduced this locally; it appears that none of the rounding policies provide `cos_down` (and `cos_up`, and others).

---

## Comment 2

> Username: mclow  
> Created at: 2018-07-04 17:49:47 UTC  
> Url: https://github.com/boostorg/interval/issues/13#issuecomment-402534076  

This functionality has been missing for a long time.

---

## Comment 3

> Username: zfergus  
> Created at: 2020-02-28 19:32:49 UTC  
> Url: https://github.com/boostorg/interval/issues/13#issuecomment-592690301  

For transcendental functions, you need to specify the rounding policy. For example:  
```  
typedef boost::numeric::interval<  
    double,  
    boost::numeric::interval_lib::policies<  
        boost::numeric::interval_lib::save_state<  
            boost::numeric::interval_lib::rounded_transc_std<double> >,  
        boost::numeric::interval_lib::checking_base<double> > >  
    Interval;  
```  
Read more about the rounding policy for transcendental functions [here](https://www.boost.org/doc/libs/1_72_0/libs/numeric/interval/doc/rounding.htm).

---

## Comment 4

> Username: njezersek  
> Created at: 2022-07-27 18:22:08 UTC  
> Url: https://github.com/boostorg/interval/issues/13#issuecomment-1197169347  

The above solution didn't work for me. But I found a working example here: https://stackoverflow.com/questions/47488323/boost-interval-with-multiprecision  
  
```  
#include <iostream>  
#include <boost/numeric/interval.hpp>  
#include <boost/numeric/interval/io.hpp>  
  
namespace bn = boost::numeric;  
namespace bni = bn::interval_lib;  
  
template <typename T>  
using Interval = bn::interval<T, bni::policies<bni::save_state<bni::rounded_transc_exact<T>>, bni::checking_base<T>>>;  
  
int main() {  
	Interval<double> x(0.1, 0.2);  
  
	Interval<double> y = sin(x);  
	std::cout << y << std::endl; // [0.0998334,0.198669]  
}  
```
