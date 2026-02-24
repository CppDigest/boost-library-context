# #252 Add files via upload [Merged]

> Username: pabristow  
> Created at: 2020-06-24 15:58:50 UTC  
> Updated at: 2020-07-21 18:27:12 UTC  
> Merged at: 2020-07-21 18:23:53 UTC  
> Closed at: 2020-07-21 18:23:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/252  

Prototype additional test for confirming roundtrip from binary to decimal (or hexadecimal) back to binary.  
  
This  brute force method uses many millions of random floating-point values output to a stringstream and read back in, taking several hours.  
  
It is templated to allow the test to run for any floating-point type, including multiprecision types and Quad aka float128.  
  
This test is derived for one use to confirm errors in the standard libraries over a decade ago.  Since then better algorithms have been developed and float and double at least have been shown to have no errors detectable by this method.  the Ryu algorithm has a mathematical proof of correctness.  
  
It is not intended that this test should be run routinely but very occasionally as deemed useful to give assurance that the code is correct.  
  
So it is not added to the jamfile.v2 yet, but could be conditionally to run 'on-demand'.  
  
With the most recent versions of the three main compilers, GCC, MSVC and Clang, no errors have been found using std::defaultfloat and std::scientific formats,  
but only MSVC passes using std::hexfloat.  
  
The test has been run with the exponent range covering all from std::numeric_limit<T>::max_exponent to min_exponent, but that barely tests the most commonly used range around zero (especially for multiprecision with massive exponent ranges), so it has also be run successful with a smaller range from e-6 to e+6.  
  
Views welcome

---

## Comment 1

> Username: pabristow  
> Created_at: 2020-06-24 16:00:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/252#issuecomment-648910601  

Of course this noob have blundered here - I want to   
  
	pabristow wants to merge 1 commit into develop from roundtrip-1  
  
:-(

---
