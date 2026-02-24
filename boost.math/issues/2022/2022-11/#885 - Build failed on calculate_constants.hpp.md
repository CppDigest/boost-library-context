# #885 - Build failed on calculate_constants.hpp [Closed]

> Username: dharanlinux  
> Created at: 2022-11-28 15:28:09 UTC  
> Updated at: 2022-12-05 15:11:56 UTC  
> Closed at: 2022-12-05 15:11:56 UTC  
> Url: https://github.com/boostorg/math/issues/885  

Facing below build issue with boost-1.80.1. Do we need to include the missing header.  
  
Check the below error log.  
  
```  
In file included from ./boost/math/constants/constants.hpp:341,  
                 from ./boost/math/special_functions/gamma.hpp:24,  
                 from ./boost/math/special_functions/detail/bessel_jy.hpp:14,  
                 from ./boost/math/special_functions/bessel.hpp:20,  
                 from ./boost/math/special_functions/airy.hpp:12,  
                 from ./boost/math/special_functions.hpp:15,  
                 from libs/math/build/../src/tr1/pch.hpp:9:  
./boost/math/constants/calculate_constants.hpp: In static member function 'static T boost::math::constants::detail::constant_plastic<T>::compute()':  
./boost/math/constants/calculate_constants.hpp:1014:15: error: 'std::cbrt' has not been declared  
    using std::cbrt;  
               ^~~~  
```

---

## Comment 1

> Username: mborland  
> Created at: 2022-11-28 16:40:11 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1329404392  

@dharanlinux Can you please test the linked patch to see if it solves your issue?

---

## Comment 2

> Username: dharanlinux  
> Created at: 2022-11-28 17:28:37 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1329474426  

@mborland Sorry it is not fixing the issue. I am using the uClibc for cross-compiling the boost.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-11-28 18:28:11 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1329552848  

@mborland those headers will have been included anyway.  
  
Two questions: are you building for C++11 or later?  And does your <cmath> implement std::cbrt or is it missing?

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-11-28 20:55:46 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1329750877  

>> using the uClibc for cross-compiling  
  
>  does your implement std::cbrt or is it missing?  
  
Hi @dharanlinux sometimes older or incomplete versions of the C/C++ standard library do not include `cbrt` within the standard namespace. We need to see if your compiler does or does not have `std::cbrt`. As John mentioned, this came in C++11. But some ports of the C++ standard library actually missed this step and do not include the fuction declaration of `std::cbrt`.  
  
This might be the case for your query.  
  
Cc: @mborland  and @jzmaddock

---

## Comment 5

> Username: mborland  
> Created at: 2022-11-28 21:31:01 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1329786045  

Looking through the [uClibc++ source](https://cxx.uclibc.org) there is no definition of `std::cbrt` provided which would explain the issue.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-11-29 05:41:37 UTC  
> Updated at: 2022-11-29 05:43:20 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1330107995  

> Looking through the [uClibc++ source](https://cxx.uclibc.org/) there is no definition of `std::cbrt`  
  
This might be a larger problem for the OP. My understanding is that this port is based on an older standard like C++03 (although this is not, as far as I can see, officially stated in the docs mentioned above).  
  
I'd like to know from @dharanlinux is an embedded system being targeted here? If so, which compiler/microcontroller is being targeted here? It sounds like you're on an embedded system?  
  
Depending on the situation there might be some other options.

---

## Comment 7

> Username: dharanlinux  
> Created at: 2022-11-29 06:28:52 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1330147949  

I am using the raspberry-pi3 board with uclibc. As you mentioned my uclibc source doesn't provide the std::cbrt.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-11-29 12:14:26 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1330538056  

@NAThompson : Looks like you added the plastic constant which is the problem here, is there any reason not to remove the using declarations here?  The rationale, is that this code is only used when calculating the constants to extended precision so the std:: versions will never be used anyway as T will be a multiprecision UDT type?

---

## Comment 9

> Username: NAThompson  
> Created at: 2022-11-29 13:33:50 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1330659994  

@jzmaddock : You're exactly right; we can remove it.

---

## Comment 10

> Username: mborland  
> Created at: 2022-12-03 20:41:55 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1336254483  

@dharanlinux can you try https://github.com/boostorg/math/pull/888 to see if it solves your issue?

---

## Comment 11

> Username: dharanlinux  
> Created at: 2022-12-05 15:07:17 UTC  
> Url: https://github.com/boostorg/math/issues/885#issuecomment-1337539848  

@mborland Thanks for the followup and fix. Yes the shared patch fix the build issue.
