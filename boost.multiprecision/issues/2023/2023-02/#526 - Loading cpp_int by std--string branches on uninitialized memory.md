# #526 - Loading cpp_int by std::string branches on uninitialized memory [Closed]

> Username: guidovranken  
> Created at: 2023-02-05 02:36:43 UTC  
> Updated at: 2023-02-06 19:28:48 UTC  
> Closed at: 2023-02-06 19:28:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/526  

Program:  
  
```cpp  
#include <boost/multiprecision/cpp_int.hpp>  
int main(void)  
{  
    using namespace boost::multiprecision;  
    const std::string v("00000000000000000000000052222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222");  
    cpp_int V(v);  
    return 0;  
}  
```  
  
Compile:  
  
```sh  
$ clang++ -I boost_1_81_0/ p.cpp -g  
```  
  
Run under valgrind:  
```  
$ valgrind ./a.out  
==2920517== Command: ./a.out  
==2920517==   
==2920517== Conditional jump or move depends on uninitialised value(s)  
==2920517==    at 0x4022B7: boost::multiprecision::backends::cpp_int_base<0ul, 18446744073709551615ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long>, false>::normalize() (cpp_int.hpp:405)  
==2920517==    by 0x401CEA: boost::multiprecision::backends::cpp_int_backend<0ul, 0ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >::do_assign_string(char const*, std::integral_constant<bool, false> const&) (cpp_int.hpp:1874)  
==2920517==    by 0x4014C7: boost::multiprecision::backends::cpp_int_backend<0ul, 0ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >::operator=(char const*) (cpp_int.hpp:1918)  
==2920517==    by 0x401418: _ZN5boost14multiprecision6numberINS0_8backends15cpp_int_backendILm0ELm0ELNS0_16cpp_integer_typeE1ELNS0_18cpp_int_check_typeE0ESaIyEEELNS0_26expression_template_optionE1EEC2INSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEERKT_PNSt9enable_ifIXaaaaoooosr5boost14multiprecision6detail13is_arithmeticISH_EE5valuesr3std7is_sameISG_SH_EE5valuesr3std14is_convertibleISH_PKcEE5valuentsr6detail25is_explicitly_convertibleINS0_6detail9canonicalISH_S7_E4typeES7_EE5valuesr6detail24is_restricted_conversionISQ_S7_EE5valueEvE4typeE (number.hpp:123)  
==2920517==    by 0x40137F: main (p.cpp:6)  
```  
  
Tested on Linux x64 with Clang++ 15 and Boost 1.81.0.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-02-05 09:50:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/526#issuecomment-1417267431  

Confirmed, gets the wrong value too, I have a fix in the works.
