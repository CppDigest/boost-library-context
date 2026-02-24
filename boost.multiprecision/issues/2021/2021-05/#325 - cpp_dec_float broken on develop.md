# #325 - cpp_dec_float broken on develop [Closed]

> Username: jzmaddock  
> Created at: 2021-05-19 15:32:36 UTC  
> Updated at: 2021-06-03 15:29:18 UTC  
> Closed at: 2021-06-03 15:29:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/325  

@ckormanyos : cpp_dec_float appears to be limited to 17-digit precision on develop, test case is:  
  
```  
   using dec35 = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<35>>;  
  
   dec35 a = tgamma(dec35(2.5));  
   dec35 b("1.3293403881791370204736256125058588870981620920917903461603558423");  
  
   std::cout << fabs((a - b) / b) << std::endl;  
```  
  
The problem appears to have been introduced in https://github.com/boostorg/multiprecision/commit/aa88310934e3f7555c8a630269a18f555af88ac0 but I don't see why/where.  
  
You can also reproduce with:  
  
```  
cd libs/multiprecision/test  
../../../b2 specfun_cpp_dec_float  
```  
  
Which shows up lots of related failures (and I must add that to one of the CI runs, we haven't done so before because it takes ages to run).

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-05-19 15:55:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/325#issuecomment-844239057  

> `cpp_dec_float` appears to be limited to 17-digit precision on develop,  
  
Oh great catch John. My bad.  
  
I think I simply got too hasty in removing `#include <boost/multiprecision/detail/big_lanczos.hpp>`.  
  
I am not sure if that is the _only_ thing that I might have unintentionally broken.  
  
I will correct in the branch then get those tests cycling.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2021-05-19 15:57:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/325#issuecomment-844240610  

I checked large digit range specifically in the multiprecision tests. But I wonder why this case slips through multiprecision's testing?  
  
I will also soon try to add explicit tests in Multiprecision for low-digit count tgamma with dec float.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-05-20 07:08:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/325#issuecomment-844774985  

This is being handled in #326

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-06-03 15:29:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/325#issuecomment-853958449  

The original issue had been the non-inclusion of the _big Lanczos_ header file, formerly known from Multiprecision.  
  
As it turned out, the re-inclusion of this header file, did, in fact, lead to recognition of some imprecise Lanczos tables in selected digit ranges such as 34 decimal digits. In this way, a related weakness was found via this unrelated bug.  
  
The Lanczos coefficients in most digit ranges have now been reworked in #326. Additional testing and verification carried out in #327.  
  
Will now close this issue.  
  
@jzmaddock Thanks again for such widespread work and excellent repairs on this issue and the scope of possible improvements to which it has brought us.
