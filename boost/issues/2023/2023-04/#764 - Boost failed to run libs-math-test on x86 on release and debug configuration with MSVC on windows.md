# #764 - Boost failed to run libs\math\test on x86 on release and debug configuration with MSVC on windows [Open]

> Username: KawasakiNinjaH2R  
> Created at: 2023-04-11 03:06:08 UTC  
> Updated at: 2023-04-11 03:06:08 UTC  
> Url: https://github.com/boostorg/boost/issues/764  

Hi all,  
  
Boost failed to run libs\math\test on x86 on release and debug configuration with MSVC on windows. it can be reproduced on ce46565 on master branch. Could you please help look at this issue?  
  
**Environment:**  
VS 2022 + Windows 10  
  
**Repro steps:**  
git clone https://github.com/boostorg/boost.git F:\gitP  
cd F:\gitP\boostorg\boost  
.\bootstrap 2>&1  
.\b2 headers variant=debug --build-dir=F:\gitP\boostorg\boost\out\x86rel address-model=32 2>&1.  
.\b2 variant=debug --build-dir=F:\gitP\boostorg\boost\out\x86rel address-model=32 2>&1  
set _CL_=/D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING  
cd F:\gitP\boostorg\boost  
set OPENSSL_ROOT=F:\tools\OpenSSL\32  
set path=F:\tools\OpenSSL\32\bin;%path%  
set path=C:\Python\Python27;%path%  
.\b2 -j16 variant=debug -d+2 --build-dir=F:\gitP\boostorg\boost\out\x86rel libs\math\test address-model=32 2>&1  
  
**Error info:**  
Test log:  
[lib_math_debug.log](https://github.com/boostorg/boost/files/11195952/lib_math_debug.log)  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 50.95 RMS Mean=2.205  
    worst case at row: 510  
    { -2.5, 1.9133758544921875, 0.037417550346939089 }  
Peak error greater than expected value of 40  
F:/gitP/boostorg/boost/libs/math/test/handle_test_result.hpp(165): error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel I'v: Mathworld Data (large values) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 50.95 RMS Mean=2.205  
    worst case at row: 510  
    { -2.5, 1.9133758544921875, 0.037417550346939089 }  
Peak error greater than expected value of 40  
F:/gitP/boostorg/boost/libs/math/test/handle_test_result.hpp(165): error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel I'v: Mathworld Data (large values) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
