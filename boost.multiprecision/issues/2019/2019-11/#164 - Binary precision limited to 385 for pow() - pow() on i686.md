# #164 - Binary precision limited to 385 for pow() * pow() on i686 [Closed]

> Username: cbehan  
> Created at: 2019-11-06 19:53:49 UTC  
> Updated at: 2019-11-14 19:20:32 UTC  
> Closed at: 2019-11-14 19:20:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/164  

I haven't seen any problem with this library on 64-bit machines. But on 32-bit, I am getting a stack overflow essentially from multiplying two numbers if the precision is sufficiently high. It appears that this was first reported at https://stackoverflow.com/questions/56245875/mysterious-stackoverflow-exception-in-boostmultiprecisiongmp-floatoperator  
  
The minimal example I can make is:  
  
    int main(void) {  
            // 385 works, 386 does not  
            const size_t precision_base_10(boost::multiprecision::detail::digits2_2_10(386));  
            boost::multiprecision::mpf_float::default_precision(precision_base_10);  
            boost::multiprecision::mpf_float res = pow((boost::multiprecision::mpf_float)2, 2) * pow((boost::multiprecision::mpf_float)2, 2);  
            std::cout << res << "\n";  
    }

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-07 19:13:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/164#issuecomment-551222036  

The issue disappears for me if you use mpfr_float rather than mpf_float.... but this is our bug, what's happening is that the precision you ask mpf for isn't the precision you actually get, and our code starts chasing it's tail trying to create a temporary with the "correct" precision to assign the expression to.  
I need to think about this for a bit, as I'm not sure how to fix correctly at present.

---

## Comment 2

> Username: cbehan  
> Created at: 2019-11-07 22:46:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/164#issuecomment-551301428  

Thanks! The code where I first ran into this was avoiding MPFR by design, but your explanation is already very helpful. I can workaround this for now by setting the precision to a multiple of 32 :).
