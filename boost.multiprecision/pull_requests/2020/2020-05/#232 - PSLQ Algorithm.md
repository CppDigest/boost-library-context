# #232 PSLQ Algorithm [Closed]

> Username: NAThompson  
> Created at: 2020-05-03 12:09:30 UTC  
> Updated at: 2021-01-30 16:10:24 UTC  
> Closed at: 2020-07-18 14:30:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/232  

Don't bother looking at this yet; I'm just making a PR so I can work on it from multiple computers and simultaneously shame myself into finishing it.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-05-16 07:25:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/232#issuecomment-629602119  

> Don't bother looking at this yet  
  
Well, against your advice, I did want to at least start compiling an example. I selected `cpp_dec_float` at 100 decimal digits and the constant `pi + ln_two`.  
  
But I ran into this error:  
`Cannot open include file: 'Eigen/Dense': No such file...`  
  
Do I need a particular branch of Boost.Math in order to find Eigen/Dense?  
Or I can ease off on this one for a while if that is preferred?  
  
```  
#include <iostream>  
#include <map>  
#include <string>  
  
#include <boost/multiprecision/pslq.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
namespace test_pslq  
{  
  using big_float_type = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<100>,  
                                                       boost::multiprecision::et_off>;  
  
  // N[Pi + Log[2], 101]  
  const std::string str_pi_plus_ln2_101  
  {  
    "3.8347398341497385478798755047376794522726695337353610750956246018012100282559037142338981523385357555"  
  };  
}  
  
int main()  
{  
  using pslq_map_type = std::map<test_pslq::big_float_type, std::string>;  
  
  std::map<test_pslq::big_float_type, std::string> m =  
    boost::multiprecision::small_pslq_dictionary<typename pslq_map_type::value_type::first_type>();  
  
  auto s = boost::multiprecision::pslq<typename pslq_map_type::value_type::first_type>(m);  
}  
```

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-05-16 12:28:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/232#issuecomment-629638130  

@ckormanyos : I'm using the [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) matrix library right now to do the matrix reductions. I hope to eliminate that dependency before merging, which I now believe to be very achievable.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-05-24 15:02:53 UTC  
> Updated_at: 2020-05-24 15:35:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/232#issuecomment-633244210  

@ckormanyos : Go ahead and give this a try. It's not super fast, but it is much faster than Mathematica and Maple, as well as returning way fewer spurious relations.  
  
I haven't been able to test the windows version; I just stackoverflow programmed it and hope for the best; would be nice to see if it works.  
  
Here's the way you'd write your example now:  
  
```cpp  
#include <boost/multiprecision/pslq.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
  
int main()  
{  
    using namespace boost::math::constants;  
    using boost::multiprecision::number;  
    using boost::multiprecision::mpfr_float_backend;  
    using boost::multiprecision::identify;  
    using Real = number<mpfr_float_backend<100> >;  
    Real max_acceptable_solution_norm = 1e5;  
    std::string s = identify<Real>({pi<Real>() + ln_two<Real>(), "(π+ln(2))"}, max_acceptable_solution_norm);  
    if (!s.empty())  
    {  
        std::cout << s << "\n";  
    }  
    else  
    {  
        std::cout << "No relations found.\n";  
    }  
}  
```  
  
and expected output:  
  
```  
$  ./main.x  
  
As  
        1⋅0.693147 + 1⋅3.14159 - 1⋅3.83474 = 0,  
it is likely that  
        1⋅ln(2) + 1⋅π - 1⋅(π+ln(2)) = 0.  
```

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-07-18 14:30:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/232#issuecomment-660491539  

I pwned this branch with a bad merge; squashed these commits into pslq_2.

---
