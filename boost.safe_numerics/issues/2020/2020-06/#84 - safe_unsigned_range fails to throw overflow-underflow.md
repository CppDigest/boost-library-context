# #84 - safe_unsigned_range fails to throw overflow/underflow [Closed]

> Username: ralf-em  
> Created at: 2020-06-15 14:07:13 UTC  
> Updated at: 2020-08-19 22:02:03 UTC  
> Closed at: 2020-07-03 15:26:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84  

setup:   
(1) MSVC, toolset 14.2, language C++17, boost 1.73.0  
(2) gcc, C++17, but older boost 1.71.0  
  
in (1) fails to note that 7 is out of range `boost::safe_numerics::safe_unsigned_range<1910, 2099> test0 = 7` however all is ok for a signed range `boost::safe_numerics::safe_signed_range<1910, 2099> test1 = 7;` or for unsigned but other MIN/MAX: `boost::safe_numerics::safe_unsigned_range<0, 2> test2 = 7;`   
  
I wonder how the behavior can possibly depend on the values of the bounds?  
  
in (2) fails to throw in all 3 cases. Code:  
  
```C++  
#include <iostream>  
#include <boost/safe_numerics/safe_integer_range.hpp>  
int main(int argc, char* argv[])  
{  
    try   
    {  
	boost::safe_numerics::safe_unsigned_range<1910, 2099> test0 = 7; // bug? no exception here  
	std::cout << "  min: " << std::numeric_limits<decltype(test0)>::min()   
			<< "  max: " << std::numeric_limits<decltype(test0)>::max() << "\n";  
	std::cout << "  test0: " << test0 << "\n";  
	boost::safe_numerics::safe_signed_range<1910, 2099> test1 = 7; // ok, msvc throws  
	std::cout << "  test1: " << test1 << "\n";  
	boost::safe_numerics::safe_unsigned_range<0, 2> test2 = 7; // ok, msvc throws	  
	std::cout << "  test2: " << test2 << "\n";  
    }  
    catch (const std::exception& e)   
    {  
	std::cout << " exception ---  " << e.what();  
    }  
    return 0;  
}  
```  
  
output (1) MSVC:  
  
```  
min: 1910  max: 2099  
test0: 7  
exception ---  converted signed value too large: positive overflow error  
```  
  
output (2) gcc  
```  
min: 1910  max: 2099  
test0: 7  
test1: 7  
test2: 7  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-06-15 15:28:03 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-644204436  

(1) certainly looks like a bug.  But is the bug in the safe numerics library or in msvc 14.2?  The boost test matrix - https://www.boost.org/development/tests/develop/developer/safe_numerics.html - shows msvc 14.2 failing a number of tests on the current develop branch.  I don't have this compiler available so I depend upon the kindness of strangers.  In this was it seems that that stranger happens to be you - thus proving yet again that no good deed gets unpunished.  If you want to experiment with this - perhaps running all the safe numeric test suite on your local setup, It might be very helpful.  
  
(2) is even more disturbing.  But it is with version 1.71.  There have been reported errors in assignment operators and I have fixed them.  So it might already be fixed.  I would be curious to know which version of gcc you're testing with.  The test matrix is currently showing no test failures for recent versions of gcc and clang.  But it's easy for me to run your test locally so I can determine whether it's been fixed or if my test suite has a hole in it.  
  
Thanks for contacting me about this.  And thanks in advance for your efforts to nail this down.  
  
Robert Ramey

---

## Comment 2

> Username: ralf-em  
> Created at: 2020-06-15 21:27:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-644399468  

Thank you for the quick reply. I installed boost 1.73 on Linux and now the results are identical to (1) msvc. Version is gcc 9.3.0. It seems you fixed something after 1.71 but not all. Also now there are 3 warnings which gcc did not produce with boost 1.71  
  
```  
1>/usr/local/boost_1_73_0/boost/safe_numerics/utility.hpp(134,55): warning : conversion from ‘int’ to ‘boost::detail::exact_unsigned_base_helper<8>::exact’ {aka ‘unsigned char’} may change value [-Wconversion]  
1>/usr/local/boost_1_73_0/boost/safe_numerics/utility.hpp(140,56): warning : conversion from ‘unsigned int’ to ‘boost::detail::exact_unsigned_base_helper<16>::exact’ {aka ‘short unsigned int’} may change value [-Wconversion]  
1>/usr/local/boost_1_73_0/boost/safe_numerics/utility.hpp(146,56): warning : conversion from ‘long unsigned int’ to ‘boost::detail::exact_unsigned_base_helper<32>::exact’ {aka ‘unsigned int’} may change value [-Wconversion]  
```  
What is the meaning of the emptiness in the test matrix for BI-msvc-14.2-64? It does not say "fail". If I can manage I will run local tests later.   
  
Thanks again, Ralf

---

## Comment 3

> Username: ralf-em  
> Created at: 2020-06-16 17:09:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-644895334  

New results from more test cases, for both, assignment to safe_signed_range, and safe_unsigned_range. All test cases should throw as all values should produce underflow or overflow error.  
  
[Test_safe_range.txt](https://github.com/boostorg/safe_numerics/files/4788085/Test_safe_range.txt)  
  
boost 1.73  
(1) windows 10, msvc 14.2, c++17, x64   
(2) ubuntu 20.04, gcc 9.3.0, c++17, x64  
we have identical results for (1) and (2), expecting 60 failures to throw:   
  
- overflow: all ok  
- underflow:   
  - safe_signed_range always fails to throw when trying to assign unsigned value  
  - safe_unsigned_range always fails to throw

---

## Comment 4

> Username: robertramey  
> Created at: 2020-06-23 21:14:34 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-648431847  

OK - I feel I've fixed this.  A simple bug.  Of course nothing is simple.  I made a new test test_custom_exception and re-organized your example so that it's easier to run and debug.  I've checked this into the develop branch.  You should pull this branch, try it out and verify that I haven't missed anything.  Once you've had a chance to do this, I'll merge into master. Please don't delay as the opportunity to merge for boost 1.74 is coming up soon.  Also I've tested with Clang and gcc but I can't test on windows so if you could that also it would be nice.  But if you can't - we're no worse off.  
  
Thanks for the time you've invested in investigating the problem and isolating things down to a test case which I can use.  It' s only due to persons such as your self that my two libraries (safe numerics and serialization) can continue to improve and be relevant.  
  
Robert Ramey

---

## Comment 5

> Username: ralf-em  
> Created at: 2020-06-30 18:13:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-651958741  

from my email, June 24:  
My colleague ran our test cases on msvc again and found that the safe_signed_range is ok now. But there seems to be no difference for the safe_unsigned_range. So in our tests we are down from 60 to 40 failures to throw. Could you pls. have another look? Thanks again.  
  
I like to use your library as it is going to save us a lot of headache. We where doing many hand-coded almost paranoid checks of function arguments, user inputs, etc. where we can now use something like using weekday = safe_unsigned_range<1,7> and just catch the exceptions.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-06-30 18:41:34 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-651973958  

"But there seems to be no difference for the safe_unsigned_range. So in our tests we are down from 60 to 40 failures to throw. Could you pls. have another look? Thanks again."  
  
Hmmm - I slightly altered your test to make it easier to run.  Maybe I broke something.  Could you please try running this and see if you get the same results:  
  
`//  
// some tests for boost::safe_numerics  
//  
// purpose: test assignment to safe_signed_range and safe_unsigned_range  
// all test cases should throw as all values should produce underflow or overflow error  
//  
// boost 1.73  
// (1) windows 10, msvc 14.2, c++17, x64  
// (2) ubuntu 20.04, gcc 9.3.0, c++17, x64  
// we have identical results for (1) and (2), expecting 60 failures to throw:  
// overflow: all ok  
// underflow:  
//    safe_signed_range always fails to throw when trying to assign unsigned value  
//    safe_unsigned_range always fails to throw  
//  
// authors: U. Kaltenborn, R. Zoeller (github: ralf-em)  
//  
  
#include <cstdint>  
#include <boost/safe_numerics/safe_integer_range.hpp>  
  
#define BOOST_TEST_MODULE safe_ranges  
#include <boost/test/included/unit_test.hpp>  
  
  
using namespace boost::safe_numerics;  
  
// use this to get cases were exception is incorrectly not thrown  
#define BUG_CASE( a1, a2 ) BOOST_CHECK_THROW( a1, a2 )  
  
// maybe use this define as long as bugs are not fixed  
//#define BUG_CASE( a1, a2 ) BOOST_CHECK_NO_THROW( a1 )  
  
BOOST_AUTO_TEST_CASE(SAFE_RANGE_ROLLOUT)  
{  
	const int8_t s1_low = 25;  
	const int8_t s1_up = 37;  
	int8_t s1_val1 = 13;  
	int8_t s1_vup1 = 99;  
  
	const int16_t s2_low = 1000;  
	const int16_t s2_up = 30000;  
	int16_t s2_val1 = 13;  
	int16_t s2_val2 = 750;  
	int16_t s2_vup2 = 31000;  
  
	const int32_t s4_low = 1000000;  
	const int32_t s4_up = 30000000;  
	int32_t s4_val1 = 13;  
	int32_t s4_val2 = 750;  
	int32_t s4_val4 = 80000;  
	int32_t s4_vup4 = 40000000;  
  
	const int64_t s8_low = 50000000000;  
	const int64_t s8_up = 70000000000;  
	int64_t s8_val1 = 13;  
	int64_t s8_val2 = 750;  
	int64_t s8_val4 = 80000;  
	int64_t s8_val8 = 40000000000;  
	int64_t s8_vup8 = 80000000000;  
  
	const uint8_t u1_low = 25;  
	const uint8_t u1_up = 37;  
	uint8_t u1_val1 = 13;  
	uint8_t u1_vup1 = 99;  
  
	const uint16_t u2_low = 1000;  
	const uint16_t u2_up = 30000;  
	uint16_t u2_val1 = 13;  
	uint16_t u2_val2 = 750;  
	uint16_t u2_vup2 = 31000;  
  
	const uint32_t u4_low = 1000000;  
	const uint32_t u4_up = 30000000;  
	uint32_t u4_val1 = 13;  
	uint32_t u4_val2 = 750;  
	uint32_t u4_val4 = 80000;  
	uint32_t u4_vup4 = 40000000;  
  
	const uint64_t u8_low = 50000000000;  
	const uint64_t u8_up = 70000000000;  
	uint64_t u8_val1 = 13;  
	uint64_t u8_val2 = 750;  
	uint64_t u8_val4 = 80000;  
	uint64_t u8_val8 = 40000000000;  
	uint64_t u8_vup8 = 80000000000;  
  
	safe_signed_range<s1_low, s1_up> s11_range;  
	safe_signed_range<s1_low, s2_up> s12_range;  
	safe_signed_range<s2_low, s2_up> s22_range;  
	safe_signed_range<s1_low, s4_up> s14_range;  
	safe_signed_range<s2_low, s4_up> s24_range;  
	safe_signed_range<s4_low, s4_up> s44_range;  
	safe_signed_range<s1_low, s8_up> s18_range;  
	safe_signed_range<s2_low, s8_up> s28_range;  
	safe_signed_range<s4_low, s8_up> s48_range;  
	safe_signed_range<s8_low, s8_up> s88_range;  
  
	safe_unsigned_range<u1_low, u1_up> u11_range;  
	safe_unsigned_range<u1_low, u2_up> u12_range;  
	safe_unsigned_range<u2_low, u2_up> u22_range;  
	safe_unsigned_range<u1_low, u4_up> u14_range;  
	safe_unsigned_range<u2_low, u4_up> u24_range;  
	safe_unsigned_range<u4_low, u4_up> u44_range;  
	safe_unsigned_range<u1_low, u8_up> u18_range;  
	safe_unsigned_range<u2_low, u8_up> u28_range;  
	safe_unsigned_range<u4_low, u8_up> u48_range;  
	safe_unsigned_range<u8_low, u8_up> u88_range;  
  
	// Cases for (partly) 1-Byte signed safe range  
  
	BOOST_CHECK_THROW(s11_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BUG_CASE(s11_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
  
	// Cases for (partly) 2-Byte signed safe range  
  
	BOOST_CHECK_THROW(s12_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
  
	BUG_CASE(s12_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(s22_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(s22_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
  
	// Cases for (partly) 4-Byte signed safe range  
  
	BOOST_CHECK_THROW(s14_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s24_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BOOST_CHECK_THROW(s24_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
	BOOST_CHECK_THROW(s44_range = s4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable (signed)  
	BOOST_CHECK_THROW(s44_range = s4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable (signed)  
	BOOST_CHECK_THROW(s44_range = s4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable (signed)  
  
	BUG_CASE(s14_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(s24_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(s24_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(s44_range = u4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(s44_range = u4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(s44_range = u4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable ( unsigned)  
  
	// Cases for (partly) 8-Byte signed safe range  
  
	BOOST_CHECK_THROW(s18_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s28_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BOOST_CHECK_THROW(s28_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
	BOOST_CHECK_THROW(s48_range = s4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable (signed)  
	BOOST_CHECK_THROW(s48_range = s4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable (signed)  
	BOOST_CHECK_THROW(s48_range = s4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable (signed)  
	BOOST_CHECK_THROW(s88_range = s8_val1, std::exception); // assigned with 1-Byte value in 8-Byte variable (signed)  
	BOOST_CHECK_THROW(s88_range = s8_val2, std::exception); // assigned with 2-Byte value in 8-Byte variable (signed)  
	BOOST_CHECK_THROW(s88_range = s8_val4, std::exception); // assigned with 4-Byte value in 8-Byte variable (signed)  
	BOOST_CHECK_THROW(s88_range = s8_val8, std::exception); // assigned with 8-Byte value in 8-Byte variable (signed)  
  
	BUG_CASE(s18_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(s28_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(s28_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(s48_range = u4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(s48_range = u4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(s48_range = u4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(s88_range = u8_val1, std::exception); // assigned with 1-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(s88_range = u8_val2, std::exception); // assigned with 2-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(s88_range = u8_val4, std::exception); // assigned with 4-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(s88_range = u8_val8, std::exception); // assigned with 8-Byte value in 8-Byte variable ( unsigned)  
  
	// Cases for (partly) 1-Byte  unsigned safe range  
  
	BUG_CASE(u11_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BUG_CASE(u11_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
  
	// Cases for (partly) 2-Byte  unsigned safe range  
  
	BUG_CASE(u12_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BUG_CASE(u22_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BUG_CASE(u22_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
  
	BUG_CASE(u12_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(u22_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(u22_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
  
	// Cases for (partly) 4-Byte  unsigned safe range  
  
	BUG_CASE(u14_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BUG_CASE(u24_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BUG_CASE(u24_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
	BUG_CASE(u44_range = s4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable (signed)  
	BUG_CASE(u44_range = s4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable (signed)  
	BUG_CASE(u44_range = s4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable (signed)  
  
	BUG_CASE(u14_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(u24_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(u24_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(u44_range = u4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(u44_range = u4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(u44_range = u4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable ( unsigned)  
  
	// Cases for (partly) 8-Byte  unsigned safe range  
  
	BUG_CASE(u18_range = s1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BUG_CASE(u28_range = s2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable (signed)  
	BUG_CASE(u28_range = s2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable (signed)  
	BUG_CASE(u48_range = s4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable (signed)  
	BUG_CASE(u48_range = s4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable (signed)  
	BUG_CASE(u48_range = s4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable (signed)  
	BUG_CASE(u88_range = s8_val1, std::exception); // assigned with 1-Byte value in 8-Byte variable (signed)  
	BUG_CASE(u88_range = s8_val2, std::exception); // assigned with 2-Byte value in 8-Byte variable (signed)  
	BUG_CASE(u88_range = s8_val4, std::exception); // assigned with 4-Byte value in 8-Byte variable (signed)  
	BUG_CASE(u88_range = s8_val8, std::exception); // assigned with 8-Byte value in 8-Byte variable (signed)  
  
	BUG_CASE(u18_range = u1_val1, std::exception); // assigned with 1-Byte value in 1-Byte variable ( unsigned)  
	BUG_CASE(u28_range = u2_val1, std::exception); // assigned with 1-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(u28_range = u2_val2, std::exception); // assigned with 2-Byte value in 2-Byte variable ( unsigned)  
	BUG_CASE(u48_range = u4_val1, std::exception); // assigned with 1-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(u48_range = u4_val2, std::exception); // assigned with 2-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(u48_range = u4_val4, std::exception); // assigned with 4-Byte value in 4-Byte variable ( unsigned)  
	BUG_CASE(u88_range = u8_val1, std::exception); // assigned with 1-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(u88_range = u8_val2, std::exception); // assigned with 2-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(u88_range = u8_val4, std::exception); // assigned with 4-Byte value in 8-Byte variable ( unsigned)  
	BUG_CASE(u88_range = u8_val8, std::exception); // assigned with 8-Byte value in 8-Byte variable ( unsigned)  
  
  
	// Result:  
	//  
	// unsigned safe range underflow never throws  
	// signed safe range underflow never throws when assigned variable is unsigned  
  
  
	BOOST_CHECK_THROW(s11_range = s1_vup1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = s2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s22_range = s2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s12_range = s2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s12_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s12_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s44_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s44_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s88_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s11_range = u1_vup1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = u2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s11_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s22_range = u2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s22_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s44_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(s44_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(s88_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
  
  
	BOOST_CHECK_THROW(u11_range = s1_vup1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = s2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u22_range = s2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u22_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u22_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u44_range = s4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u44_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u88_range = s8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u11_range = u1_vup1, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = u2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u11_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u22_range = u2_vup2, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u22_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u22_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u44_range = u4_vup4, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
	BOOST_CHECK_THROW(u44_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	BOOST_CHECK_THROW(u88_range = u8_vup8, std::exception); // assigned with 1-Byte value in 1-Byte variable (signed)  
  
	// Result:  
	//  
	// overflow ok  
}  
`  
  
Which results in the following output:  
  
Running 1 test case...  
  
[1;32;49m*** No errors detected  
[0;39;49mProgram ended with exit code: 0  
  
So I'm see all test cases pass.  (I'm not sure what the weird output is ([1;32;49m***) - perhaps the time

---

## Comment 7

> Username: robertramey  
> Created at: 2020-06-30 18:45:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-651975758  

Also - I reran your original test - after tweaking the error reporting to my taste:  
  
`#include <iostream>  
#include <functional>  
#include <boost/safe_numerics/safe_integer_range.hpp>  
#include <boost/safe_numerics/interval.hpp> // print variable range  
#include <boost/safe_numerics/native.hpp> // native promotion policy  
#include <boost/safe_numerics/exception.hpp> // safe_numerics_error  
#include <boost/safe_numerics/exception_policies.hpp>  
  
// log an exception condition but continue processing as though nothing has happened  
// this would emulate the behavior of an unsafe type.  
struct log_runtime_exception {  
    log_runtime_exception() = default;  
    void operator () (  
        const boost::safe_numerics::safe_numerics_error & e,  
        const char * message  
    ){  
        std::cout  
            << "Caught system_error with code "  
            << boost::safe_numerics::literal_string(e)  
            << " and message " << message << '\n';  
    }  
};  
  
// logging policy  
// log arithmetic errors but ignore them and continue to execute  
// implementation defined and undefined behavior is just executed  
// without logging.  
  
using logging_exception_policy = boost::safe_numerics::exception_policy<  
    log_runtime_exception,  // arithmetic error  
    log_runtime_exception,  // implementation defined behavior  
    log_runtime_exception,  // undefined behavior  
    log_runtime_exception   // uninitialized value  
>;  
  
template<unsigned int Min, unsigned int Max>  
using sur = boost::safe_numerics::safe_unsigned_range<  
    Min, // min value in range  
    Max, // max value in range  
    boost::safe_numerics::native, // promotion policy  
    logging_exception_policy // exception policy  
>;  
  
template<int Min, int Max>  
using ssr = boost::safe_numerics::safe_signed_range<  
    Min, // min value in range  
    Max, // max value in range  
    boost::safe_numerics::native, // promotion policy  
    logging_exception_policy // exception policy  
>;  
  
int main() {  
    const sur<1910, 2099> test0 = 7; // note logged exception - value too low  
    std::cout << "test0 = " << test0 << '\n';  
    const ssr<1910, 2099> test1 = 7; // note logged exception - value too low  
    std::cout << "test1 = " << test1 << '\n';  
    const sur<0, 2> test2 = 7;       // note logged exception - value too large  
    std::cout << "test2 = " << test2 << '\n';  
    const sur<1910, 2099> test3;     // unitialized value  
    std::cout << "test3 = " << test3 << '\n';  
    sur<1910, 2099> test4 = 2000;   // OK value  
    std::cout << "test4 = " << test4 << boost::safe_numerics::make_interval(test4) << '\n';  
    return 0;  
}  
`  
which yields the following output:  
`Caught system_error with code domain_error and message converted value to low or negative  
test0 = 4832  
Caught system_error with code negative_overflow_error and message converted signed value too small  
test1 = 5312  
Caught system_error with code positive_overflow_error and message converted signed value too large  
test2 = 64  
Caught system_error with code uninitialized_value and message safe values must be initialized  
test3 = 0  
test4 = 2000[1910,2099]  
Program ended with exit code: 0  
`  
which looks good to me.  
  
So somewhere we're not on the same page.  Could you try running the above code and see if you have any differences?

---

## Comment 8

> Username: robertramey  
> Created at: 2020-06-30 18:55:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-651980810  

"I like to use your library as it is going to save us a lot of headache. We where doing many hand-coded almost paranoid checks of function arguments, user inputs, etc."  
  
Right now that's the only alternative.  But it's tedious, makes the code very hard to verify by review and is error prone.  
  
"where we can now use something like using weekday = safe_unsigned_range<1,7> and just catch the exceptions."  
  
I had never thought of this - very nice example.  
  
It is currently urgent for me to get all this resolved so it can be merged into the next boost release which is coming up soon.  And of course it's urgent for you for other reasons.  So at least on this we are in sync.    
  
I would like to add your second test to the official test suite.  But I don't want to add a dependency on Boost.Test.  I had problems with this before because the developers were fiddling with Boost.Test at the same time I was trying to run my own tests so I was debugging both my stuff and their stuff simultaneously.  A very frustrating situation!  So I switched to "Light weight test" for the serialization library and just used "naked testing" for this library.  This has worked pretty well.  I'm not adverse to using light weight test and perhaps it would be a good idea.  In short, if you want to make your second test not depend on boost.test, I would gladly incorporate it into the safe numerics test suite.  
  
Speaking of testing, you can see that the the tests are pretty exhaustive.  Unfortunately, it's not so easy to do this level of testing for ranges as ranges are even more varied.  If someone could figure out how to do the testing we do now on values on ranges, that might be very interesting.  
  
Thanks for using and helping me debug this library.  As you can see, I've gone to great pains to make this thing reliable and as efficient as possible.  Without efforts such as those by you and your team, I could not do this.  I very much appreciate them.  
  
Robert Ramey

---

## Comment 9

> Username: ulle-em  
> Created at: 2020-07-01 11:41:42 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-652368840  

@robertramey Your latest test routine gives the same results here.  
   
This is the expected outcome, since the test examples only have signed integers being assinged to the safe ranges. Those cases have worked fine in our tests when using your latest version.  
  
After changing the first lines of your main program to  
  
	uint16_t seven = 7;  
	const sur<1910, 2099> test0 = seven; // note logged exception - value too low  
	std::cout << "test0 = " << test0 << '\n';  
	const ssr<1910, 2099> test1 = seven; // note logged exception - value too low  
	std::cout << "test1 = " << test1 << '\n';  
  
the test result becomes (when using windows)  
  
test0 = 7  
test1 = 7  
Caught system_error with code positive_overflow_error and message converted signed value too large  
test2 = 120  
Caught system_error with code uninitialized_value and message safe values must be initialized  
test3 = 55050  
test4 = 2000[1910,2099]  
  
which also is in line with our test results. The problem occurs when assigning an unsigned integer to a safe range in the underflow case (assigned integer is smaller than lower bound of range).  
  
I also re-ran your version of our long test you had posted, which really seems to be the same stuff as our original version. On a windows machine there are still 40 failures (having #define BUG_CASE( a1, a2 ) BOOST_CHECK_THROW( a1, a2 )).   
  
I am not sure my colleague did test your latest version with linux. If not, then maybe the reason for the different  
results are now due to windows/linux differences?  
  
Hope that helps.  
  
Ulrich

---

## Comment 10

> Username: robertramey  
> Created at: 2020-07-01 15:06:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-652474768  

I'm thinking I'm getting mixed up between signed and unsigned tests.  Hang in there.  These things haven't been hard to fix, just hard to find.

---

## Comment 11

> Username: robertramey  
> Created at: 2020-07-02 00:01:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-652703226  

I reran my tests with the same results.  I also did a git pull to ensure that my current version is the same as that in the git server.  Are you absolutely sure that you did a git pull or update before re-running the tests.  If you are, I would be curious to see your version of the file "checked_integer.hpp"  as this is were all the changes have been made.

---

## Comment 12

> Username: ulle-em  
> Created at: 2020-07-03 13:39:16 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-653553612  

We pulled the latest version of safe_numerics today, which has some differences in checked_integer.hpp compared to the version we had used to perform the tests before.  With that latest version all our tests, including your small test posted above, were successful. safe_numeric routines now throw exactly when they are expected to throw. As far as we are concerned the issue can be closed.

---

## Comment 13

> Username: GregKon  
> Created at: 2020-08-19 15:25:18 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-676494696  

Dear Robert  
  
Does this patch go to 1.74 release?  
I am asking because there is no mentioned about hit in changelog:  
https://www.boost.org/users/history/version_1_74_0.html

---

## Comment 14

> Username: robertramey  
> Created at: 2020-08-19 15:57:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-676512861  

It should be there.  I'm derelict adding entries to the change log.  This is especially true for bug fixes since bug fixes are just adjusting the code to match the documentation.  
  
FYI - here's my general procedure to keep my sanity.  
  
a) user reports bug, with hopefully a PR  
b) I investigate the bug and perhaps ask for more info  
c) I make a test program.  and verify that the test fails.  
d) Note that my testing tests my develop branch against the release branch of other boost libraries.  This is so I'm testing only one thing at a time and don't spend a lot of time isolating bugs in the develop branch of other libraries.  
e) once fixed, I may add the test program to the official test suite in order to end the whack-a-mole situation.  
f) I check everything in to develop branch and watch CI and Boost test matrix.  
g) As release date approaches, I merge all changes since the last release into the Master branch.  
h) I cross my fingers.

---

## Comment 15

> Username: ralf-em  
> Created at: 2020-08-19 16:53:48 UTC  
> Updated at: 2020-08-19 22:02:03 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-676542441  

I am afraid it is not in 1.74: I just compared the folder contents \boost_1_74_0\boost\safe_numerics with 1.73, ---> identical :(

---

## Comment 16

> Username: robertramey  
> Created at: 2020-08-19 19:53:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/84#issuecomment-676628845  

OK - I see this now.  I'll merge develop into master. before monday.  They will be identical  
 Sorry about this.  In my defense, I've got two libraries this one and serialization.  They don't require a lot of time to maintain, but each change is quite subtle and it's sometimes hard to remember what I did to what.
