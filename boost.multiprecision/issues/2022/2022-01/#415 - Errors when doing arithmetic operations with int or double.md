# #415 - Errors when doing arithmetic operations with int or double [Closed]

> Username: afabri  
> Created at: 2022-01-20 16:08:31 UTC  
> Updated at: 2022-01-24 09:08:14 UTC  
> Closed at: 2022-01-24 09:08:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/415  

Hello,  
  
When using the GMP backend we encounter problems for code where we do a multiplication with an `int` of value zero or a subtraction with a `double`.   This concerns Visual C++ and we observe it with boost 1_78_0.      
  
I have isolated the problem and put it in this [gist](https://gist.github.com/afabri/c977d9e14b925d83269ec76366aa1673).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-20 17:58:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/415#issuecomment-1017771358  

I get zero as expected (0/1 if we're being pedantic) for the multiply by zero, but the subtract double is definitely a bug our end.  I'll take care of that shortly.  
  
What do you see for the multiply by zero, and which MPIR version are you using?

---

## Comment 2

> Username: mglisse  
> Created at: 2022-01-20 21:39:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/415#issuecomment-1017951355  

> What do you see for the multiply by zero,  
  
I don't know what happens on real windows, but with wine and mingw, it crashes with a division by 0 in eval_multiply `b /= mpz_get_ui(g.data());`. Note that if I use a larger power of 2 as denominator, I get the crash on linux just as well.  
  
It is the case where the gcd does not fit unsigned long and get_ui returns 0, which can happen when `b` is 0 and the denominator is a multiple of 2^64 (or 2^32). I also don't see what mpz_fits_uint_p is for since there are no `unsigned int` neaby (maybe `mpz_fits_ulong_p`? but the condition still looks wrong). Special casing `b==0` may be the easiest solution, and then I expect the gcd always fits unsigned long. mpz_divexact_ui may be better than mpz_div.

---

## Comment 3

> Username: afabri  
> Created at: 2022-01-21 08:21:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/415#issuecomment-1018283027  

It is here:  
```  
inline void eval_multiply(gmp_rational& result, gmp_rational const& a, unsigned long b)  
{  
   gmp_int g;  
   mpz_gcd_ui(g.data(), mpq_denref(a.data()), b);  
   if (!mpz_fits_uint_p(g.data()) || (mpz_get_ui(g.data()) != 1))  
   {  
      b /= mpz_get_ui(g.data());   //   HERE  
      ```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-01-24 09:08:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/415#issuecomment-1019871478  

Correct on all counts, fixed in https://github.com/boostorg/multiprecision/pull/417
