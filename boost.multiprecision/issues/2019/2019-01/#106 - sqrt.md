# #106 - sqrt [Closed]

> Username: ohhmm  
> Created at: 2019-01-20 12:36:59 UTC  
> Updated at: 2019-01-20 12:44:56 UTC  
> Closed at: 2019-01-20 12:44:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/106  

boost::multiprecision::cpp_int i = 32;  
    auto sq = boost::multiprecision::sqrt(i);  
    BOOST_TEST(sq*sq==i); // fails

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-20 12:44:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/106#issuecomment-455863544  

Of course that fails - sqrt on integer types performs integer square-root which in this case is 5, and 5*5 != 32.  
In case you're wondering integer square root is important for certain number-theoretic algorithms, including implementing floating-point square root.
