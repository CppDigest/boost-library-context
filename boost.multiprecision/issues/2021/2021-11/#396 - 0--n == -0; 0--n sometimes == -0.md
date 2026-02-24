# #396 - 0/-n == -0; 0*-n sometimes == -0 [Closed]

> Username: mgeck64  
> Created at: 2021-11-25 07:50:25 UTC  
> Updated at: 2021-12-04 10:30:24 UTC  
> Closed at: 2021-12-04 10:28:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396  

The following program illustrates and reproduces this issue:  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_complex.hpp>  
  
// built with Boost version 1.78.0 beta 1  
  
int main() {  
    {  
        using FT = boost::multiprecision::cpp_bin_float_50;  
        const auto n = FT(6.66); // any real number  
        const auto neg_n = -n;  
  
        // output of these three cases is -0; should be 0:  
        std::cout << (0/-n) << std::endl;  
        std::cout << (0*neg_n) << std::endl;  
        std::cout << (0/neg_n) << std::endl;  
      
        // curiously, output of this case is 0:  
        std::cout << (0*-n) << std::endl;  
    }  
  
    //--------------------------------------------------------------------------  
    // try with complex type; get the same result. complex type was where the  
    // issue was originally discovered  
  
    {  
        using CT = boost::multiprecision::cpp_complex_50;  
        const auto n  = CT(3.14159, 0); // any positive real number  
        const auto neg_n  = -n;  
  
        // output of these three cases is -0; should be 0:  
        std::cout << (0/-n) << std::endl;  
        std::cout << (0*neg_n) << std::endl;  
        std::cout << (0/neg_n) << std::endl;  
      
        // curiously, output of this case is 0:  
        std::cout << (0*-n) << std::endl;  
    }  
}  
```

---

## Comment 1

> Username: mgeck64  
> Created at: 2021-11-25 15:03:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-979285871  

I added three more scenarios to the program that produce different results from the original program! Here's the complete new program:  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_complex.hpp>  
  
// built with Boost version 1.78.0 beta 1  
  
int main() {  
    {  
        using FT = boost::multiprecision::cpp_bin_float_50;  
        const auto n = FT(6.66); // any real number  
        const auto neg_n = -n;  
  
        // output of these three cases is -0; should be 0:  
        std::cout << (0/-n) << std::endl;  
        std::cout << (0*neg_n) << std::endl;  
        std::cout << (0/neg_n) << std::endl;  
      
        // curiously, output of this case is 0:  
        std::cout << (0*-n) << std::endl;  
    }  
  
    //--------------------------------------------------------------------------  
    // try with complex type; get the same result. complex type was where the  
    // issue was originally discovered  
  
    {  
        using CT = boost::multiprecision::cpp_complex_50;  
        const auto n  = CT(3.14159, 0); // any positive real number  
        const auto neg_n  = -n;  
  
        std::cout << std::endl;  
  
        // output of these three cases is -0; should be 0:  
        std::cout << (0/-n) << std::endl;  
        std::cout << (0*neg_n) << std::endl;  
        std::cout << (0/neg_n) << std::endl;  
      
        // curiously, output of this case is 0:  
        std::cout << (0*-n) << std::endl;  
    }  
  
    //--------------------------------------------------------------------------  
    // scenarios where we make 0 a constant of FT or CT or CT::value_type first  
    // (results differ from above!):  
  
    {  
        using FT = boost::multiprecision::cpp_bin_float_50;  
        const auto _0 = FT(0);  
        const auto n = FT(6.66); // any real number  
        const auto neg_n = -n;  
  
        std::cout << std::endl;  
  
        // output of all four cases is -0:  
        std::cout << (_0/-n) << std::endl;  
        std::cout << (_0*neg_n) << std::endl;  
        std::cout << (_0/neg_n) << std::endl;  
        std::cout << (_0*-n) << std::endl;  
    }  
  
    {  
        using CT = boost::multiprecision::cpp_complex_50;  
        const auto _0 = CT(0);  
        const auto n  = CT(3.14159, 0); // any positive real number  
        const auto neg_n  = -n;  
  
        std::cout << std::endl;  
  
        // output of these two cases is -0:  
        std::cout << (_0/-n) << std::endl;  
        std::cout << (_0/neg_n) << std::endl;  
      
        // output of these two cases is 0:  
        std::cout << (_0*neg_n) << std::endl;  
        std::cout << (_0*-n) << std::endl;  
    }  
  
    {  
        using CT = boost::multiprecision::cpp_complex_50;  
        const auto _0 = CT::value_type(0);  
        const auto n  = CT(3.14159, 0); // any positive real number  
        const auto neg_n  = -n;  
  
        std::cout << std::endl;  
  
        // output of all cases is -0:  
        std::cout << (_0/-n) << std::endl;  
        std::cout << (_0/neg_n) << std::endl;  
        std::cout << (_0*neg_n) << std::endl;  
        std::cout << (_0*-n) << std::endl;  
    }  
}  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-11-27 12:00:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-980547589  

All of your test cases should result in minus-zero, however, I'm not sure if that holds for complex types, investigating that now.

---

## Comment 3

> Username: mgeck64  
> Created at: 2021-11-27 17:15:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-980723742  

Are you saying that -0 should be the correct/expected result for all cases?  
  
Not all cases are providing consistent results when I run the program. Here's my output:  
```  
-0  
-0  
-0  
0  
  
-0  
-0  
-0  
0  
  
-0  
-0  
-0  
-0  
  
-0  
-0  
0  
0  
  
-0  
-0  
-0  
-0  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-11-27 17:57:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-980771963  

Yes there's a small but in the multiplication routine which doesn't correctly propagate the sign when one argument is zero.  
  
IEEE mostly specifies what should happen (see also https://en.wikipedia.org/wiki/Signed_zero), but there are corner cases - for example +0 + -0 has no specified sign.  So.... I'm working on a test program that tests all the permutations and also checks that we are consistent with IEEE doubles.  
  
Complex values are more.... complex.  I've already confirmed that msvc std::complex does not always do the right thing with basic arithmetic and signed zeros, and then we have things like `sqrt(-4+0i) = 2i`, but `sqrt(-4-0i) = -2i`, at least if we do things right anyway.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-12-02 19:42:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-984944949  

OK, so the referenced PR fixes scalar arithmetic, complex arithmetic, or at least division is causing me problems, consider:  
  
(0+0i) / (0-2i)  
  
MPC gives:  
  
(0+0i)  
  
Which agrees with using the formula  
  
(a+bi)/(c+di) = (ac + bd) / (c^2 + d^2) + i * (bc - ad) / (c^2 + d^2)  
  
However, both std::complex and Boost's complex_adaptor give:  
  
(-0+0i)  
  
Which is the result of applying:  
  
(a+bi)/(c+di) = (a * (c/d) + b) / (c * (c/d) + d) + i * (b * c/d - a) / (c * (c/d) + d)  
  
and which is algebraically equivalent, but differs only when in the presence of signed zeros.  
  
Note that both answers are correct, in that they are multiplicative inverses of the denominator, and both also rely on +0-0 = +0 which is not strictly defined by the IEEE rules for signed zeros so far as I can tell.  Likewise C99 annex G appears not to be overly helpful.  
  
So.... at present, unless you're aware of a definitive reference on this, I'm inclined to leave alone for now, even though mpc_complex and cpp_complex/std::complex will sometimes differ.  
  
Thoughts?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-12-04 10:30:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/396#issuecomment-986005433  

OK, this should now be fixed.  
  
There are still warts: for example dividing by a scalar can produce a different result (in the sign of zero) than dividing by (x+0i).  But that's an inevitable result of signed-zero arithmetic.
