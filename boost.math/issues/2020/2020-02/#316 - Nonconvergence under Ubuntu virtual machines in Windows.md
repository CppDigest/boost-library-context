# #316 - Nonconvergence under Ubuntu virtual machines in Windows. [Closed]

> Username: JaapAap  
> Created at: 2020-02-22 23:32:41 UTC  
> Updated at: 2024-02-22 10:41:02 UTC  
> Closed at: 2024-02-22 10:41:02 UTC  
> Url: https://github.com/boostorg/math/issues/316  

Using GCC, the call non_central_chi_squared<double>::find_non_centrality(1, 15.903178413033118, 0.001) only works when internal promotion from double to long double is prevented. When the promotion is performed, the tolerance setting for the root-finding algorithm is lowered which prevents the algorithm from converging within the 200 iterations that are set as the default maximum. It took me quite some time to figure this out (with MSVC all is well as it does not perform such promotion).  
  
It seems that the tolerance setting might be overly aggressive for this case. Is this something that could be addressed generically - I didn't yet test this on other input values but I would expect the values used well within the 'normal' range and would hope the standard policies should suffice?

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-03-03 19:09:45 UTC  
> Updated at: 2020-03-03 19:10:59 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594117152  

I just tried this:  
  
```cpp  
#include <iostream>  
#include <boost/math/distributions/non_central_chi_squared.hpp>  
  
int main()  
{  
    using boost::math::non_central_chi_squared;  
    double x = non_central_chi_squared::find_non_centrality(1, 15.903178413033118, 0.001);  
    std::cout << "x = " << x << "\n";  
// Output:  
// x = 50.0997  
}  
```  
  
I didn't see any issue; is the value wrong?  
  
(I compiled with `g++-9`. . . )

---

## Comment 2

> Username: JaapAap  
> Created at: 2020-03-03 19:54:31 UTC  
> Updated at: 2020-03-03 21:27:00 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594137911  

Thanks for trying! The value seems fine. I am using GCC v7.4.0 on Ubuntu - I am unsure whether that could make a difference as I am not a regular GCC user (but it does seem unlikely). Would you be able to check whether internal promotion to long doubles happens in your case - I can provide the relevant lines of code if needed?

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-03-03 20:14:43 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594146523  

Please provide a `main` that reproduces your problem. I can retry with gcc 7.4 on Ubuntu but it won't be for a few days.

---

## Comment 4

> Username: JaapAap  
> Created at: 2020-03-03 20:38:48 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594156759  

The main you provided above reproduces it. The "x=" line raises an exception with the following message:  "Error in function non_central_chi_squared<long double>::find_non_centrality: Unable to locate solution in a reasonable time: or there is no answer to problem.  Current best guess is 50.0996587650940483627"

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-03-03 20:43:46 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594158860  

Ok, I'll try it on Ubuntu in a couple days; thanks for the issue report!

---

## Comment 6

> Username: JaapAap  
> Created at: 2020-03-03 21:36:49 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594182212  

.... maybe also relevant: i am targeting a 64-bit architecture.

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-03-04 00:34:45 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594243809  

All your compiler flags are relevant.

---

## Comment 8

> Username: JaapAap  
> Created at: 2020-03-04 08:20:04 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594385385  

I think this is the list being used (as subtracted from the VS-generated g++ call, I removed all -W flags):  
-fpic  
-std=c++17  
-fno-strict-aliasing  
-g2  
-gdwarf-2  
-O0  
-fthreadsafe-statics  
-frtti  
-fno-omit-frame-pointer  
-std=c11  
-fexceptions

---

## Comment 9

> Username: JaapAap  
> Created at: 2020-03-04 12:13:30 UTC  
> Updated at: 2020-03-05 12:05:39 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-594485611  

Alternative main:  
  
```  
#include <iostream>  
#include <boost/math/distributions/non_central_chi_squared.hpp>  
  
int main()  
{  
   using boost::math::non_central_chi_squared_distribution;  
   using namespace boost::math::policies;  
  
   try {  
      using my_policy = policy<promote_double<false>>;  
  
      double x = non_central_chi_squared_distribution<double, my_policy>::find_non_centrality(1, 15.903178413033118, 0.001);  
      std::cout << "x = " << x << "\n";  
  
      double y = non_central_chi_squared_distribution<double>::find_non_centrality(1, 15.903178413033118, 0.001);  
      std::cout << "y = " << y << "\n";  
   }  
   catch (const std::exception & e) {  
      std::cout << "exception = " << e.what() << "\n";  
   }  
   catch (...) {  
      std::cout << "exception" << "\n";  
   }  
   // output  
   // x = 50.0997  
   // exception = Error in function non_central_chi_squared<long double>::find_non_centrality: Unable to locate solution in a   
   //             reasonable time: or there is no answer to problem. Current best guess is 50.0996587650940483627  
  
}  
  
```

---

## Comment 10

> Username: NAThompson  
> Created at: 2020-03-05 14:52:42 UTC  
> Updated at: 2020-03-05 14:58:40 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595269326  

@JaapAap : I was not able to reproduce with `g++-7`:  
  
```cpp  
$ g++-7 --version  
g++-7 (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0  
$  g++-7 --std=gnu++17 -O3 -I../../  -o main.x main.cpp  
$ ./main.x  
x = 50.0997  
```  
  
I get the same result when using your compiler flags. What commit are you on? I'm on 12bd7424a02afc6661f00c85a2c198322a385b19

---

## Comment 11

> Username: JaapAap  
> Created at: 2020-03-05 15:00:10 UTC  
> Updated at: 2020-03-05 15:01:23 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595273516  

GCC is the exact same version as reported by you above. Boost is regular v1.69 distribution.

---

## Comment 12

> Username: NAThompson  
> Created at: 2020-03-05 15:21:43 UTC  
> Updated at: 2020-03-05 15:22:28 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595285765  

Bah! This is frustrating:  
  
```  
$  git checkout boost-1.69.0  
math git:(dff5644a1) ✗ make DEBUG=1 CXX=g++-7 main.x && ./main.x  
g++-7 --std=gnu++17 -fno-finite-math-only -march=native -O2 -I../../  -o main.x main.cpp  
x = 50.0997  
```  
  
Could you give me the result of `lscpu`?

---

## Comment 13

> Username: JaapAap  
> Created at: 2020-03-05 15:25:57 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595288033  

It is - just tried it the Linux-only way (using your command line in the WSL/Ubuntu console on my Windows box) and I do see the exception being raised. I believe that in this case it uses boost v1.65 which is installed on Ubuntu. I am not a Linux or GCC user - just porting code from Windows for a project, so let me know what I could try to further assist.

---

## Comment 14

> Username: NAThompson  
> Created at: 2020-03-05 15:33:06 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595291706  

Could you tell me what `lscpu` says?  
  
I just checked out the boost 1.65:  
  
```  
$ git checkout boost-1.65.1   
➜  math git:(5d12dab37) ✗ make CXX=g++-7 main.x && ./main.x  
g++-7 --std=gnu++17  -O2 -I../../  -o main.x main.cpp  
In file included from ../../boost/detail/endian.hpp:9:0,  
                 from ../../boost/math/special_functions/detail/fp_traits.hpp:27,  
                 from ../../boost/math/special_functions/sign.hpp:18,  
                 from ../../boost/lexical_cast/detail/inf_nan.hpp:34,  
                 from ../../boost/lexical_cast/detail/converter_lexical_streams.hpp:63,  
                 from ../../boost/lexical_cast/detail/converter_lexical.hpp:54,  
                 from ../../boost/lexical_cast/try_lexical_convert.hpp:44,  
                 from ../../boost/lexical_cast.hpp:32,  
                 from ../../boost/math/tools/convert_from_string.hpp:15,  
                 from ../../boost/math/constants/constants.hpp:13,  
                 from ../../boost/math/special_functions/gamma.hpp:24,  
                 from ../../boost/math/distributions/non_central_chi_squared.hpp:14,  
                 from main.cpp:2:  
../../boost/predef/detail/endian_compat.h:11:161: note: #pragma message: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead  
 use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
                                                                                                                                            ^  
x = 50.0997  
```  
  
No exception! Something truly weird is happening.

---

## Comment 15

> Username: JaapAap  
> Created at: 2020-03-05 15:38:12 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595294309  

lscpu says:  
  
Architecture:        x86_64  
CPU op-mode(s):      32-bit, 64-bit  
Byte Order:          Little Endian  
CPU(s):              8  
On-line CPU(s) list: 0-7  
Thread(s) per core:  2  
Core(s) per socket:  4  
Socket(s):           1  
Vendor ID:           GenuineIntel  
CPU family:          6  
Model:               94  
Model name:          Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz  
Stepping:            3  
CPU MHz:             4008.000  
CPU max MHz:         4008.0000  
BogoMIPS:            8016.00  
Virtualization:      VT-x  
Hypervisor vendor:   Windows Subsystem for Linux  
Virtualization type: container  
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave osxsave avx f16c rdrand lahf_lm abm 3dnowprefetch fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm mpx rdseed adx smap clflushopt intel_pt ibrs ibpb stibp ssbd

---

## Comment 16

> Username: jzmaddock  
> Created at: 2020-03-05 16:55:32 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595334271  

I've just checked this with cpp_bin_float at various precisions - and even at 100 decimal digit precision only 19 iterations of the root finder are required (double extended requires 16 iterations).  So it shouldn't be throwing.  Will try on Ubuntu later.

---

## Comment 17

> Username: JaapAap  
> Created at: 2020-03-05 17:14:49 UTC  
> Updated at: 2020-03-05 17:41:05 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595344397  

I suppose all depends on the tolerance level that is used versus the accuracy that is actually achieved. When using 64-bit (long) doubles (~16 significant digits), tolerance is set to 8.9e-16, and reached after 16 iterations. When internal promotion to 80-bit version (18 to 19 significant digits) happens, tolerance is set to 4.3e-19 (which is close to the number of significant digits) and 200 iterations don't suffice. I am not sure what the tolerance levels are doing for extended precision floating point numbers...

---

## Comment 18

> Username: NAThompson  
> Created at: 2020-03-05 17:20:40 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595347143  

Ok, I have no clue what's going on. But I've thought for some time I need to relativize the tolerance on the real Newton's method rootfinder by the condition number of rootfinding. So maybe that'll help.  
  
@jzmaddock : Is there any hope that'll fix this?

---

## Comment 19

> Username: jzmaddock  
> Created at: 2020-03-05 17:44:12 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595357897  

> I am not sure what the tolerance levels are doing for extended precision floating point numbers...  
  
What I'm saying is you can reach better than 10^-100 precision in about 19 iterations, so there's something else going on here.

---

## Comment 20

> Username: jzmaddock  
> Created at: 2020-03-05 17:58:03 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595363970  

Just tried your test case on Ubuntu-19.10 and gcc-9.2.1 and everything is working just fine.  
  
I suspect something is going wrong before it even reaches the root finder, what happens if you build with -DBOOST_MATH_INSTRUMENT  ?  If you run the long double with promotion test only then the diagnostic output should look like:  
  
```  
boost/boost/math/special_functions/erf.hpp:1203 result_type = e  
boost/boost/math/special_functions/erf.hpp:1204 value_type = e  
boost/boost/math/special_functions/erf.hpp:1205 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1214 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
lgamma_imp called with e N5boost4math7lanczos12lanczos17m64E  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/special_functions/erf.hpp:1168 result_type = e  
boost/boost/math/special_functions/erf.hpp:1169 value_type = e  
boost/boost/math/special_functions/erf.hpp:1170 precision_type = N5boost4math8policies7digits2ILi64EEE  
boost/boost/math/special_functions/erf.hpp:1179 tag_type = N5boost17integral_constantIiLi64EEE  
boost/boost/math/special_functions/erf.hpp:404 64-bit precision erf_imp called  
boost/boost/math/tools/toms748_solve.hpp:549 a = 14.903178413033117521990789100527763 b = 29.806356826066235043981578201055527 fa = 0.54969420153125124337478366043363565 fb = 0.069558937624932744751913692854428106 count = 198  
boost/boost/math/tools/toms748_solve.hpp:549 a = 29.806356826066235043981578201055527 b = 59.612713652132470087963156402111053 fa = 0.069558937624932744751913692854428106 fb = -0.00090541175879605505065477751664923534 count = 197  
boost/boost/math/tools/toms748_solve.hpp:350  a = 29.806356826066235043981578201055527 b = 59.229725287190252779045973596794283  
boost/boost/math/tools/toms748_solve.hpp:362  a = 29.806356826066235043981578201055527 b = 51.664540803323699504068233778752983  
boost/boost/math/tools/toms748_solve.hpp:261  a = 29.806356826066235043981578201055527 b = 51.664540803323699504068233778752983 d = 59.229725287190252779045973596794283 e = 59.612713652132470087963156402111053 fa = 0.069558937624932744751913692854428106 fb = -0.00031268578398730910097431427642662716 fd = -0.0008956336410542684951091506827779154 fe = -0.00090541175879605505065477751664923534  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = -35.080091366536000969944542049461234 q21 = -4.057868319901426733698618365409061 q31 = 21.760365391399402927238848626245726 d21 = -11.623052803767980008242677314456159 d31 = -0.09781858585806153439957131082160835  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = 12.374491425631097983074691004645729 q32 = -3.9097087313472137486830559893746795 d32 = 0.050341002696151450758292600351495594 q33 = 12.165794732324208943761989498710818 c = 59.822808218442633167166722074625795  
boost/boost/math/tools/toms748_solve.hpp:285 Out of bounds interpolation, falling back to quadratic interpolation. c = 50.735010122767104104313240497958759  
boost/boost/math/tools/toms748_solve.hpp:398  a = 29.806356826066235043981578201055527 b = 50.735010122767104104313240497958759  
boost/boost/math/tools/toms748_solve.hpp:261  a = 29.806356826066235043981578201055527 b = 50.735010122767104104313240497958759 d = 51.664540803323699504068233778752983 e = 59.229725287190252779045973596794283 fa = 0.069558937624932744751913692854428106 fb = -0.00014064838085450925693864517441837581 fd = -0.00031268578398730910097431427642662716 fe = -0.0008956336410542684951091506827779154  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = -4.057868319901426733698618365409061 q21 = -0.75993349582216236263532485595284527 q31 = 20.886420890910080647509539097939069 d21 = -1.6894641763787577624445282453713446 d31 = -0.042232405790788411504139933036472421  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = 0.44121683634914542546231683295410164 q32 = -0.71448927217090104149765927843418467 d32 = 0.0032118178604729096386078421657139792 q33 = 0.43243700462583837014073247229095642 c = 50.410725449431253018806042831556624  
boost/boost/math/tools/toms748_solve.hpp:418  a = 29.806356826066235043981578201055527 b = 50.410725449431253018806042831556624  
boost/boost/math/tools/toms748_solve.hpp:443  a = 29.806356826066235043981578201055527 b = 50.368458053740222481187149838888217  
boost/boost/math/tools/toms748_solve.hpp:444  tol = -0.68985623931375714314043548491817148  
boost/boost/math/tools/toms748_solve.hpp:460 Not converging: Taking a bisection!!!!  
boost/boost/math/tools/toms748_solve.hpp:461  a = 40.087407439903228762584364019971872 b = 50.368458053740222481187149838888217  
boost/boost/math/tools/toms748_solve.hpp:261  a = 40.087407439903228762584364019971872 b = 50.368458053740222481187149838888217 d = 29.806356826066235043981578201055527 e = 50.410725449431253018806042831556624 fa = 0.0085497617348821311689588062221467624 fb = -6.2010606537638282893470058473525675e-05 fd = 0.069558937624932744751913692854428106 fe = -7.1419170773676125391094817677212214e-05  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = 20.583234925519502706015018134166894 q21 = -0.018314435542835919412793434929431768 q31 = 10.207020070630241453252251560002151 d21 = 20.543786792131151518192577754007289 d31 = -0.074030543206752265391191840382401068  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = -0.25999744666880948351845616317579157 q32 = -0.0078079967213458098707400481270496373 d32 = -0.063524104385262155848714937106391787 q33 = -0.19484572730381547766057535875372508 c = 50.091773786508308929227295536179554  
boost/boost/math/tools/toms748_solve.hpp:398  a = 50.091773786508308929227295536179554 b = 50.368458053740222481187149838888217  
boost/boost/math/tools/toms748_solve.hpp:261  a = 50.091773786508308929227295536179554 b = 50.368458053740222481187149838888217 d = 40.087407439903228762584364019971872 e = 29.806356826066235043981578201055527 fa = 1.8771491160488703798644364965919618e-06 fb = -6.2010606537638282893470058473525675e-05 fd = 0.0085497617348821311689588062221467624 fe = 0.069558937624932744751913692854428106  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = 1.4407756185886262458332660041371298 q21 = -0.074030543206752265391191840382401068 q31 = 0.0081295331530250917725975799005277977 d21 = 10.207020070630241453252251560002151 d31 = -0.26855473407888846019064485459715286  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = -0.0078079967213458098707400481270496373 q32 = -4.2718278339157814138416310815863676e-05 d32 = -0.19456690915047535262632008579153364 q33 = -5.0400969359893394259567434566879449e-06 c = 50.0998555612860588734380762332421  
boost/boost/math/tools/toms748_solve.hpp:418  a = 50.091773786508308929227295536179554 b = 50.0998555612860588734380762332421  
boost/boost/math/tools/toms748_solve.hpp:443  a = 50.099462323010193219585683976902146 b = 50.0998555612860588734380762332421  
boost/boost/math/tools/toms748_solve.hpp:444  tol = -7.8491516202369173335606783995692728e-06  
boost/boost/math/tools/toms748_solve.hpp:261  a = 50.099462323010193219585683976902146 b = 50.0998555612860588734380762332421 d = 50.091773786508308929227295536179554 e = 50.368458053740222481187149838888217 fa = 4.6725249092481935578834162381411943e-08 fb = -4.6807375507613809529857719360368407e-08 fd = 1.8771491160488703798644364965919618e-06 fe = -6.2010606537638282893470058473525675e-05  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = 0.0081295331530250917725975799005277977 q21 = -0.00019661913793282778348611501993475556 q31 = 0.00019644649630094946469881587839973608 d21 = 0.0078851556398171164273872262712172798 d31 = -0.00019679177956470438769344046155401057  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = -1.8460246435846895630647289170842359e-07 q32 = -4.4070247327905380417774796409271156e-09 d32 = -1.7704865660939474536398792462814944e-07 q33 = -5.6875398685394671034073579854912597e-12 c = 50.099658765093781895294533512696944  
boost/boost/math/tools/toms748_solve.hpp:398  a = 50.099658765093781895294533512696944 b = 50.0998555612860588734380762332421  
boost/boost/math/tools/toms748_solve.hpp:261  a = 50.099658765093781895294533512696944 b = 50.0998555612860588734380762332421 d = 50.099462323010193219585683976902146 e = 50.091773786508308929227295536179554 fa = 6.2489855683686007514765492487640586e-17 fb = -4.6807375507613809529857719360368407e-08 fd = 4.6725249092481935578834162381411943e-08 fe = 1.8771491160488703798644364965919618e-06  
boost/boost/math/tools/toms748_solve.hpp:269 q11 = 0.0001962653512623492310095203237214033 q21 = -0.00019679177956470438769344046155401057 q31 = 2.6273136454892360711586222499238094e-13 d21 = 0.00019644649630094946469881587839973608 d31 = -0.00019679619201424677899526716808595653  
boost/boost/math/tools/toms748_solve.hpp:277 q22 = -4.4070247327905384101313461684472521e-09 q32 = -5.9011635216452382646061586132611523e-18 d32 = -4.4124495482924653485012449223007931e-09 q33 = -1.8059084115323771857135816982097895e-22 c = 50.099658765094044619164970200131393  
boost/boost/math/tools/toms748_solve.hpp:604 max_iter = 15 count = 11  
y = 50.099658765094041257270873757079244  
```

---

## Comment 21

> Username: JaapAap  
> Created at: 2020-03-05 18:04:39 UTC  
> Updated at: 2020-03-05 21:52:14 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595367129  

I rather see this:  
  
```  
/usr/include/boost/math/special_functions/erf.hpp:1107 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1108 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1109 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1129 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
lgamma_imp called with e N5boost4math7lanczos12lanczos17m64E  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/special_functions/erf.hpp:1061 result_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1062 value_type = e  
/usr/include/boost/math/special_functions/erf.hpp:1063 precision_type = N5boost4math8policies7digits2ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:1083 tag_type = N4mpl_4int_ILi64EEE  
/usr/include/boost/math/special_functions/erf.hpp:388 64-bit precision erf_imp called  
/usr/include/boost/math/tools/toms748_solve.hpp:541 a = 14.903178413033117521990789100527763 b = 29.806356826066235043981578201055527 fa = 0.5496942015312511475855217213393189 fb = 0.069558937624932801191413034302968299 count = 198  
/usr/include/boost/math/tools/toms748_solve.hpp:541 a = 29.806356826066235043981578201055527 b = 59.612713652132470087963156402111053 fa = 0.069558937624932801191413034302968299 fb = -0.00090541175879605502365560232291841203 count = 197  
/usr/include/boost/math/tools/toms748_solve.hpp:344  a = 29.806356826066235043981578201055527 b = 59.229725287190255755831458372995257  
/usr/include/boost/math/tools/toms748_solve.hpp:356  a = 29.806356826066235043981578201055527 b = 51.66454080332369613870469038374722  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 29.806356826066235043981578201055527 b = 51.66454080332369613870469038374722 d = 59.229725287190255755831458372995257 e = 59.612713652132470087963156402111053 fa = 0.069558937624932801191413034302968299 fb = -0.00031268578398730860392479291576250944 fd = -0.00089563364105426854222535837379837176 fe = -0.00090541175879605502365560232291841203  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = -35.080091366535995689446281176060438 q21 = -4.0578683199014191629316883336286992 q31 = 21.760365391399396628457907354459167 d21 = -11.623052803767979668236876023001969 d31 = -0.097818585858061274374009030907473061  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = 12.374491425631067187396183726377785 q32 = -3.9097087313472065339681194018339738 d32 = 0.050341002696151319895090381351110409 q33 = 12.165794732324178184512675215955824 c = 59.822808218442602878894831519573927  
/usr/include/boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 50.735010122767100426699471427127719  
/usr/include/boost/math/tools/toms748_solve.hpp:392  a = 29.806356826066235043981578201055527 b = 50.735010122767100426699471427127719  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 29.806356826066235043981578201055527 b = 50.735010122767100426699471427127719 d = 51.66454080332369613870469038374722 e = 59.229725287190255755831458372995257 fa = 0.069558937624932801191413034302968299 fb = -0.000140648380854508790911705506943008 fd = -0.00031268578398730860392479291576250944 fe = -0.00089563364105426854222535837379837176  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = -4.0578683199014191629316883336286992 q21 = -0.75993349582216018767155674140667543 q31 = 20.886420890910077474700301536358893 d21 = -1.6894641763787558996767756980261765 d31 = -0.042232405790788234145605173353033024  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = 0.44121683634914254934500377203221433 q32 = -0.71448927217089897023782896212651394 d32 = 0.0032118178604728930825018551331595518 q33 = 0.43243700462583556376472415649914183 c = 50.410725449431254219234688207507133  
/usr/include/boost/math/tools/toms748_solve.hpp:412  a = 29.806356826066235043981578201055527 b = 50.410725449431254219234688207507133  
/usr/include/boost/math/tools/toms748_solve.hpp:437  a = 29.806356826066235043981578201055527 b = 50.368458053740226887384778819978237  
/usr/include/boost/math/tools/toms748_solve.hpp:438  tol = -0.68985623931375728012937997846165672  
/usr/include/boost/math/tools/toms748_solve.hpp:454 Not converging: Taking a bisection!!!!  
/usr/include/boost/math/tools/toms748_solve.hpp:455  a = 40.087407439903230965683178510516882 b = 50.368458053740226887384778819978237  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 40.087407439903230965683178510516882 b = 50.368458053740226887384778819978237 d = 29.806356826066235043981578201055527 e = 50.410725449431254219234688207507133 fa = 0.0085497617348821233762556914825836429 fb = -6.2010606537639271010342589818264969e-05 fd = 0.069558937624932801191413034302968299 fe = -7.1419170773676424711362553665594533e-05  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = 20.583234925519505509328155312687159 q21 = -0.018314435542836200038196992068151303 q31 = 10.207020070630242969400569563731551 d21 = 20.543786792131154328444608836434782 d31 = -0.074030543206753521290330866122531006  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = -0.25999744666883262134149390476522967 q32 = -0.00780799672134593111122446984495582 d32 = -0.063524104385263263639060937748581637 q33 = -0.19484572730383734051606836601422401 c = 50.091773786508291266272863140329719  
/usr/include/boost/math/tools/toms748_solve.hpp:392  a = 50.091773786508291266272863140329719 b = 50.368458053740226887384778819978237  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 50.091773786508291266272863140329719 b = 50.368458053740226887384778819978237 d = 40.087407439903230965683178510516882 e = 29.806356826066235043981578201055527 fa = 1.8771491160525771019207397216632671e-06 fb = -6.2010606537639271010342589818264969e-05 fd = 0.0085497617348821233762556914825836429 fe = 0.069558937624932801191413034302968299  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = 1.4407756185886237965121381421340629 q21 = -0.074030543206753521290330866122531006 q31 = 0.0081295331530411955867077011816945742 d21 = 10.207020070630242969400569563731551 d31 = -0.26855473407889446368912444995658007  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = -0.00780799672134593111122446984495582 q32 = -4.2718278339243268774527012565656037e-05 d32 = -0.19456690915048019396849099393875804 q33 = -5.0400969359994148423324833441228066e-06 c = 50.099855561286055660730198724195361  
/usr/include/boost/math/tools/toms748_solve.hpp:412  a = 50.091773786508291266272863140329719 b = 50.099855561286055660730198724195361  
/usr/include/boost/math/tools/toms748_solve.hpp:437  a = 50.099462323010193642858212115243077 b = 50.099855561286055660730198724195361  
/usr/include/boost/math/tools/toms748_solve.hpp:438  tol = -7.8491516201643426076540316360663496e-06  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 50.099462323010193642858212115243077 b = 50.099855561286055660730198724195361 d = 50.091773786508291266272863140329719 e = 50.368458053740226887384778819978237 fa = 4.6725249092625931179867393439053558e-08 fb = -4.6807375507452661511642588720860658e-08 fd = 1.8771491160525771019207397216632671e-06 fe = -6.2010606537639271010342589818264969e-05  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = 0.0081295331530411955867077011816945742 q21 = -0.0001966191379321400841198586029179296 q31 = 0.00019644649629977452070725418842300769 d21 = 0.0078851556398322544816359425112750614 d31 = -0.00019679177956224337838440907599846241  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = -1.8460246435855951859902521726419788e-07 q32 = -4.4070246875282403521855582645330024e-09 d32 = -1.7704865479082251896472905219881433e-07 q33 = -5.6875412378950398566350984956567656e-12 c = 50.099658765093778356458642520010471  
/usr/include/boost/math/tools/toms748_solve.hpp:392  a = 50.099658765093778356458642520010471 b = 50.099855561286055660730198724195361  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 50.099658765093778356458642520010471 b = 50.099855561286055660730198724195361 d = 50.099462323010193642858212115243077 e = 50.091773786508291266272863140329719 fa = 6.3317406873153458946035243570804596e-17 fb = -4.6807375507452661511642588720860658e-08 fd = 4.6725249092625931179867393439053558e-08 fe = 1.8771491160525771019207397216632671e-06  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = 0.00019626535126303373433517829305117175 q21 = -0.00019679177956224337838440907599846241 q31 = 2.6621070772638525592430067820900815e-13 d21 = 0.00019644649629977452070725418842300769 d31 = -0.00019679619201109355272269474390611776  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = -4.4070246875292933531053382687631137e-09 q32 = -5.9793115062526887223029736326177165e-18 d32 = -4.4124488561536497394544205363775546e-09 q33 = -1.8296058038839970985372759828729698e-22 c = 50.099658765094041257270873757079244  
/usr/include/boost/math/tools/toms748_solve.hpp:412  a = 50.099658765094041257270873757079244 b = 50.099855561286055660730198724195361  
/usr/include/boost/math/tools/toms748_solve.hpp:437  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:438  tol = -1.418258633440347831983337708192453e-16  
/usr/include/boost/math/tools/toms748_solve.hpp:263  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102 d = 50.099855561286055660730198724195361 e = 50.099658765093778356458642520010471 fa = 6.5052130349130266040447168052196503e-19 fb = -1.0842021724855044340074528008699417e-18 fd = -4.6807375507452661511642588720860658e-08 fe = 6.3317406873153458946035243570804596e-17  
/usr/include/boost/math/tools/toms748_solve.hpp:271 q11 = -0.00019679619201109355272269474390611776 q21 = -4.5584025295790618636500568696778254e-15 q31 = 2.6645352591003756970167160034179688e-15 d21 = -0.00019679619201185645158136416910110711 d31 = -4.4408920985006261616945266723632812e-15  
/usr/include/boost/math/tools/toms748_solve.hpp:279 q22 = 1.2843415129114902148043319476180956e-17 q32 = -1.6331408879298356104860527013352523e-27 d32 = 1.1751043107680256266649639762439051e-16 q33 = 1.0865088160659617406116274972315281e-18 c = 50.099658765094041257270873757079244  
/usr/include/boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:392  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:400 Can't take cubic step!!!!  
/usr/include/boost/math/tools/toms748_solve.hpp:412  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:437  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:438  tol = -1.418258633440347831983337708192453e-16  
/usr/include/boost/math/tools/toms748_solve.hpp:454 Not converging: Taking a bisection!!!!  
/usr/include/boost/math/tools/toms748_solve.hpp:455  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:378 Can't take cubic step!!!!  
/usr/include/boost/math/tools/toms748_solve.hpp:392  a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
/usr/include/boost/math/tools/toms748_solve.hpp:400 Can't take cubic step!!!!  
...  
/usr/include/boost/math/tools/toms748_solve.hpp:596 max_iter = 201 count = 197  
exception = Error in function non_central_chi_squared<long double>::find_non_centrality: Unable to locate solution in a reasonable time: or there is no answer to problem.  Current best guess is 50.0996587650940483627  
  
```

---

## Comment 22

> Username: jzmaddock  
> Created at: 2020-03-05 19:47:06 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595413452  

There's something really strange going on here: as soon as your 2 endpoints of the range get within 10^-16 of each other the endpoints just stop moving altogether, this is at:  
  
a = 50.099658765094041257270873757079244 b = 50.099658765094048362698231358081102  
  
At present I can't see why this could be - unless there's something calling a double rather than long double precision function (or one of the std:: functions isn't actually implemented for long double).  
  
Line 459 of toms_748_solve.hpp contains:  
```  
      detail::bracket(f, a, b, T(a + (b - a) / 2), fa, fb, d, fd);  
```  
Can you set a breakpoint there and try to figure out why the range [a,b] doesn't get updated during the `bracket` call?  Note that gdb doesn't really support long double very well, so may have to resort to the odd print statement :(  
  
BTW it would help if you could use the latest and greatest Boost version as well just in case... though to be honest I don't remember fixing anything in there for a long while!

---

## Comment 23

> Username: JaapAap  
> Created at: 2020-03-05 20:45:00 UTC  
> Updated at: 2020-03-07 13:09:31 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-595437799  

When calling  
`detail::bracket(f, a, b, T(a + (b - a) / 2), fa, fb, d, fd);`  
the following condition holds (see below):  
b == T(a + (b - a) / 2)  
Within the bracket routine this implies b == c and then line 115 is executed  
`c = b - fabs(b) * tol;`  
without changing the value of c (see below). The value of tol is 2.17e-19. This indeed implies that the value of b does not change, as the condition `boost::math::sign(fa) * boost::math::sign(fc) < 0` holds and b is set to c to update the interval.   
  
Some additional instrumentation gave me:  
(1) In the call to detail::bracket:  
a = 50.099658765094041257270873757079244   
b = 50.099658765094048362698231358081102  
T(a + (b - a) / 2) = 50.099658765094048362698231358081102  
  
(2) The value of c before and after executing line 115:  
c_old = 50.099658765094048362698231358081102   
c = 50.099658765094048362698231358081102

---

## Comment 24

> Username: JaapAap  
> Created at: 2020-03-07 13:12:12 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596087031  

There could be some truth in the remark that double precision is involved somewhere as I get:  
```  
b-a = 7.10542735760100185871124267578125e-15  
std::nextafter(a,b) - a = 3.4694469519536141888238489627838135e-18  
std::nextafter((double)a,(double)b) - (double)a = 7.10542735760100185871124267578125e-15  
```

---

## Comment 25

> Username: jzmaddock  
> Created at: 2020-03-07 17:28:19 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596111242  

>the following condition holds (see below):  
>b == T(a + (b - a) / 2)  
  
That's the bit I really don't understand - for that to be true we would need `(b-a)/2 == (b-a)` ?!  
  
Ok, quick sanity check - are you by any chance running on some sort of emulator?  One that has "fake" long doubles which use double precision for all arithmetic?

---

## Comment 26

> Username: jzmaddock  
> Created at: 2020-03-07 17:31:31 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596111571  

Valgrind is one such emulator which can cause strange behaviour BTW.

---

## Comment 27

> Username: JaapAap  
> Created at: 2020-03-07 18:03:42 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596121687  

I am developing in Visual Studio, the code is running on a virtual machine (Windows Subsystem for Linux (WSL) running Ubuntu), so I guess that would be a 'maybe'. Would you know of a simple way to test?

---

## Comment 28

> Username: jzmaddock  
> Created at: 2020-03-07 19:32:57 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596130055  

Is it this: https://github.com/microsoft/WSL/issues/830 ?

---

## Comment 29

> Username: JaapAap  
> Created at: 2020-03-07 20:08:23 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596133015  

I think it is - I do see the same problem reported there too. Thanks for pointing that out!

---

## Comment 30

> Username: NAThompson  
> Created at: 2020-03-08 19:40:44 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596243656  

@JaapAap, @jzmaddock : Should this issue be closed? There might not be much that we can do about it . . .

---

## Comment 31

> Username: JaapAap  
> Created at: 2020-03-08 20:03:37 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596246121  

Yes, I will close it. Thanks for your assistance!

---

## Comment 32

> Username: pabristow  
> Created at: 2020-03-09 09:37:01 UTC  
> Url: https://github.com/boostorg/math/issues/316#issuecomment-596424410  

This is pretty obscure and very hard to trace (as this and similar threads show).  
  
It could hit almost anywhere in Boost.Math ?  
  
Is there no way we can detect this and warn the hapless user?  
  
Do I understand correctly that the 'fix' is to use WSL-2?  
  
At the very least we could document it, perhaps as a FAQ?  
  
(It reminds me of a rude joke about emulation, but I won't lower the tone of the thread by repeating it here ;-)
