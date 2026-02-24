# #544 - Inaccurate Non-Central Student's T Test Data [Closed]

> Username: jzmaddock  
> Created at: 2021-02-21 13:08:49 UTC  
> Updated at: 2021-03-09 12:11:04 UTC  
> Closed at: 2021-03-09 12:11:04 UTC  
> Url: https://github.com/boostorg/math/issues/544  

This is mostly for future reference... with fixes coming soon....  
  
While testing Math on platforms with 128-bit long doubles (s390x) all the non-central T tests fail with large errors.  
  
Main test case is:  
  
v = 45487064  
l = 4.548706300511184963397681713104248e-09  
t = -0.594260692596435546875  
  
And when calculating the CDF we see:  
  
```  
Found:    0.27616888228432564767719590104835779  
Expected: 0.27616888228432562302216979333024938  
                            ^ Differ here  
```  
  
Increasing the precision of the calculation yields:  
  
```  
35 digit precision:  0.27616888228432564767719590104835779  
100 digit precision: 0.27616888228432564767719590104835781  
```  
  
So nearly in agreement, but that doesn't prove the test data is in error, and Wolfram Alpha declined to give an answer :(  
  
So.... as a first start I coded up a naive version of the CDF from wikipedia here: https://en.wikipedia.org/wiki/Noncentral_t-distribution.  
  
![image](https://user-images.githubusercontent.com/5011768/108624564-94033f80-743d-11eb-89e9-50ebe0266b71.png)  
  
Code:  
  
```  
big_t nc_t_F(big_t v, big_t nc, big_t t)  
{  
   unsigned j = 0;  
   big_t sum = 0;  
   big_t tol = std::numeric_limits<big_t>::epsilon();  
   do  
   {  
      big_t x = t * t / (t * t + v);  
      big_t p = exp(-nc * nc / 2) * pow(nc * nc / 2, j) / (2 * boost::math::factorial<big_t>(j));  
      big_t q = (nc / 2) * exp(-nc * nc / 2) * pow(nc * nc / 2, j) / (boost::math::constants::root_two<big_t>() * boost::math::tgamma(j + big_t(3) / 2));  
      big_t term = p * boost::math::ibeta(big_t(j) + 0.5, v / 2, x) + q * boost::math::ibeta(big_t(j + 1), v / 2, x);  
      ++j;  
  
      sum += term;  
  
      if (fabs(sum * tol) > fabs(term))  
         break;  
   } while (true);  
  
   return sum + boost::math::constants::half<big_t>() * (1 + boost::math::erf(-nc / boost::math::constants::root_two<big_t>()));  
}  
big_t nc_t(big_t v, big_t nc, big_t t)  
{  
   return t < 0 ? 1 - nc_t_F(v, -nc, t) : nc_t_F(v, nc, t);  
}  
```  
  
And this gave a value in agreement with our current implementation.  However, they share the same incomplete beta code, so there could still be an issue lurking somewhere.  
  
I also tried this formula:  
  
![image](https://user-images.githubusercontent.com/5011768/108624605-cf9e0980-743d-11eb-9155-1afc82ec44cd.png)  
  
But could get no more than a handful of digits agreeing :(  
  
But then I realised we have some rather fine integrators (with thanks to @NAThompson ) so I tried integrating:  
  
![image](https://user-images.githubusercontent.com/5011768/108624645-1986ef80-743e-11eb-9d55-f02ee14fab83.png)  
  
But got nonsense answers :(  
  
However, using the Wolfram definition:  
  
![image](https://user-images.githubusercontent.com/5011768/108624672-40ddbc80-743e-11eb-872f-c420d0797300.png)  
  
I got complete agreement with our code... whew!  
  
Here's the code for future reference:  
  
```  
big_t nct_A(big_t v, big_t x, big_t nu)  
{  
   big_t result = boost::math::hypergeometric_1F1(v / 2 + 1, big_t(3) / 2, nu * nu * x * x / (2 * (v + x * x)));  
   result *= boost::math::constants::root_two<big_t>() * nu * x;  
   result /= (v + x * x) * boost::math::tgamma((v + 1) / 2);  
   return result;  
}  
big_t nct_B(big_t v, big_t x, big_t nu)  
{  
   big_t result = boost::math::hypergeometric_1F1((v + 1) / 2, big_t(1) / 2, nu * nu * x * x / (2 * (x * x + v)));  
   result /= sqrt(v + x * x) * boost::math::tgamma(v / 2 + 1);  
   return result;  
}  
big_t nct_PDF(big_t v, big_t nu, big_t x)  
{  
   big_t result = nct_A(v, x, nu) + nct_B(v, x, nu);  
   result *= pow(v, v / 2) * boost::math::tgamma(v + 1);  
   result /= pow(big_t(2), v) * exp(nu * nu / 2) * pow(v + x * x, v / 2) * boost::math::tgamma(v / 2);  
   return result;  
}  
```  
  
With integration via:  
  
```  
   boost::math::quadrature::exp_sinh<big_t> integrator;  
   std::cout << integrator.integrate([&](big_t y) { return nct_PDF(big_t(v), big_t(nc), y); }, -std::numeric_limits<big_t>::infinity(), big_t(x)) << std::endl;  
```  
  
This was actually the one time I was glad to have a multiprecision type with a 64-bit long long exponent available - in cases like this you really do want the code to be as naive and close to the formulae as possible and not transformed into logs or whatever as that just makes it harder to check for correctness with the old mark-1 eyeball.  
  
Anyhow, I'll fix the test data shortly, probably using the integral unless it proves too slow.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-02-21 15:58:59 UTC  
> Url: https://github.com/boostorg/math/issues/544#issuecomment-782880325  

Wait, if the test data is generated by the integral, then doesn't it not matter much how long it takes to run? You're just gonna dump a string in there right?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-21 16:17:55 UTC  
> Url: https://github.com/boostorg/math/issues/544#issuecomment-782882966  

> Wait, if the test data is generated by the integral, then doesn't it not matter much how long it takes to run? You're just gonna dump a string in there right?  
  
Yes, but I have a hunch that some values might take a very very long time to calculate.... we'll see.
