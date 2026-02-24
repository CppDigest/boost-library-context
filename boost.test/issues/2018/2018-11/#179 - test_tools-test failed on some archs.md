# #179 - test_tools-test failed on some archs [Closed]

> Username: BinCaoWR  
> Created at: 2018-11-09 08:21:59 UTC  
> Updated at: 2020-04-21 14:23:35 UTC  
> Closed at: 2020-04-21 14:22:12 UTC  
> Url: https://github.com/boostorg/test/issues/179  

Test log:  
../libs/test/test/writing-test-ts/test_tools-test.cpp(363): error: in "test_BOOST_CHECK_EQUAL": check ots().match_pattern() has failed. Mismatch at position 721 (line 8, column 69): 'e' != 'f' :  
... ailed [0xfe != 0xfd] ...  
... ailed [0xfffffffe != ...  
  
Test code:  
TEST_CASE( test_BOOST_CHECK_EQUAL )  
{  
…  
char ch1 = -2;  
char ch2 = -3;  
BOOST_CHECK_EQUAL(ch1, ch2);  
}  
  
Potential solution:  
This is because char is unsigned on some archs.  
The test code can be fixed by changing the type of the variables ‘ch1’ & ‘ch2’, from ‘char’ to ‘signed char’.  
Then adding the following code into test_tools-test.cpp:  
namespace boost{ namespace test_tools{ namespace tt_detail{  
template<>  
struct print_log_value<signed char> {  
    void    operator()( std::ostream& ostr, signed char t )  
  {  
    ostr << std::hex  
#if BOOST_TEST_USE_STD_LOCALE  
        << std::showbase  
#else  
        << "0x"  
#endif  
        << static_cast<int>(t);  
  }  
};  
}}}  
  
Verified the test can pass with the solution.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-21 14:22:12 UTC  
> Url: https://github.com/boostorg/test/issues/179#issuecomment-617211851  

Fixed on master
