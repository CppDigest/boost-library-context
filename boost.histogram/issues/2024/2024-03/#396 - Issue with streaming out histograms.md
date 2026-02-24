# #396 - Issue with streaming out histograms [Closed]

> Username: taehyounpark  
> Created at: 2024-03-30 04:27:18 UTC  
> Updated at: 2024-03-30 05:25:41 UTC  
> Closed at: 2024-03-30 05:25:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/396  

Hello,  
  
I'd like to report a possible issue with the streaming feature of the histogram; following one of the examples to print out the histogram reports this compilation error from macOS homebrew's installation:  
```sh  
base ❯ brew list boost  
/usr/local/Cellar/boost/1.82.0_1/include/boost/ (15673 files)  
```  
  
```cpp  
#include <boost/histogram.hpp>  
  
int main() {  
  
  using namespace boost::histogram;  
  
  std::ostringstream os;  
  
  // width of histogram can be set like this; if it is not set, the library attempts to  
  // determine the terminal width and choses the histogram width accordingly  
  os.width(78);  
  
  auto h1 = make_histogram(axis::regular<>(5, -1.0, 1.0, "axis 1"));  
  h1.at(0) = 2;  
  h1.at(1) = 4;  
  h1.at(2) = 3;  
  h1.at(4) = 1;  
  
  // 1D histograms are rendered as an ASCII drawing  
  os << h1;  
}  
```  
  
```sh  
/Users/taehyounpark/hep/adl/queryosity/examples/example-hello_world.cxx:37:6: error: invalid operands to binary expression ('std::ostringstream' (aka 'basic_ostringstream<char>') and 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>'))  
  os << h1;  
  ~~ ^  ~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cstddef:125:3: note: candidate function template not viable: no known conversion from 'std::ostringstream' (aka 'basic_ostringstream<char>') to 'byte' for 1st argument  
  operator<< (byte  __lhs, _Integer __shift) noexcept  
  ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:792:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'char' for 2nd argument  
operator<<(basic_ostream<_CharT, _Traits>& __os, char __cn)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:825:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'char' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, char __c)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:832:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'signed char' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, signed char __c)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:839:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'unsigned char' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, unsigned char __c)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:853:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const char *' for 2nd argument  
operator<<(basic_ostream<_CharT, _Traits>& __os, const char* __strn)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:899:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const char *' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, const char* __str)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:906:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const signed char *' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, const signed char* __str)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:914:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const unsigned char *' for 2nd argument  
operator<<(basic_ostream<char, _Traits>& __os, const unsigned char* __str)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1099:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const error_code' for 2nd argument  
operator<<(basic_ostream<_CharT, _Traits>& __os, const error_code& __ec)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/thread:219:1: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to '__thread_id' for 2nd argument  
operator<<(basic_ostream<_CharT, _Traits>& __os, __thread_id __id)  
^  
/usr/local/include/boost/assert/source_location.hpp:137:55: note: candidate function template not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const source_location' for 2nd argument  
template<class E, class T> std::basic_ostream<E, T> & operator<<( std::basic_ostream<E, T> & os, source_location const & loc )  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:785:1: note: candidate template ignored: deduced conflicting types for parameter '_CharT' ('char' vs. 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>'))  
operator<<(basic_ostream<_CharT, _Traits>& __os, _CharT __c)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__random/uniform_int_distribution.h:258:1: note: candidate template ignored: could not match 'uniform_int_distribution' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os,  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:846:1: note: candidate template ignored: could not match 'const _CharT *' against 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>')  
operator<<(basic_ostream<_CharT, _Traits>& __os, const _CharT* __str)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1082:1: note: candidate template ignored: could not match 'basic_string' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os,  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1090:1: note: candidate template ignored: could not match 'basic_string_view' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os,  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1107:1: note: candidate template ignored: could not match 'shared_ptr' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os, shared_ptr<_Yp> const& __p)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1126:1: note: candidate template ignored: could not match 'bitset' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os, const bitset<_Size>& __x)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1074:11: note: candidate template ignored: requirement 'integral_constant<bool, false>::value' was not satisfied [with _Stream = std::ostringstream &, _Tp = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>]  
_Stream&& operator<<(_Stream&& __os, const _Tp& __x)  
          ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:1119:1: note: candidate template ignored: could not match 'unique_ptr' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os, unique_ptr<_Yp, _Dp> const& __p)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/iomanip:362:1: note: candidate template ignored: could not match '__iom_t8' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os, const __iom_t8<_MoneyT>& __x)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/iomanip:482:1: note: candidate template ignored: could not match '__iom_t10' against 'histogram'  
operator<<(basic_ostream<_CharT, _Traits>& __os, const __iom_t10<_CharT>& __x)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/valarray:4156:1: note: candidate template ignored: substitution failure [with _Expr1 = std::ostringstream, _Expr2 = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>]: no type named 'value_type' in 'std::ostringstream'  
operator<<(const _Expr1& __x, const _Expr2& __y)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/valarray:4171:1: note: candidate template ignored: substitution failure [with _Expr = std::ostringstream]: no type named 'value_type' in 'std::ostringstream'  
operator<<(const _Expr& __x, const typename _Expr::value_type& __y)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/valarray:4187:1: note: candidate template ignored: requirement '__is_val_expr<boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>>::value' was not satisfied [with _Expr = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>]  
operator<<(const typename _Expr::value_type& __x, const _Expr& __y)  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:221:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'ostream &(*)(ostream &)' for 1st argument  
    basic_ostream& operator<<(basic_ostream& (*__pf)(basic_ostream&))  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:225:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'basic_ios<char_type, traits_type> &(*)(basic_ios<char_type, traits_type> &)' (aka 'basic_ios<char, std::char_traits<char>> &(*)(basic_ios<char, std::char_traits<char>> &)') for 1st argument  
    basic_ostream& operator<<(basic_ios<char_type, traits_type>&  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:230:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'ios_base &(*)(ios_base &)' for 1st argument  
    basic_ostream& operator<<(ios_base& (*__pf)(ios_base&))  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:233:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'bool' for 1st argument  
    basic_ostream& operator<<(bool __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:234:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'short' for 1st argument  
    basic_ostream& operator<<(short __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:235:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'unsigned short' for 1st argument  
    basic_ostream& operator<<(unsigned short __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:236:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'int' for 1st argument  
    basic_ostream& operator<<(int __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:237:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'unsigned int' for 1st argument  
    basic_ostream& operator<<(unsigned int __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:238:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'long' for 1st argument  
    basic_ostream& operator<<(long __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:239:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'unsigned long' for 1st argument  
    basic_ostream& operator<<(unsigned long __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:240:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'long long' for 1st argument  
    basic_ostream& operator<<(long long __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:241:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'unsigned long long' for 1st argument  
    basic_ostream& operator<<(unsigned long long __n);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:242:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'float' for 1st argument  
    basic_ostream& operator<<(float __f);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:243:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'double' for 1st argument  
    basic_ostream& operator<<(double __f);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:244:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'long double' for 1st argument  
    basic_ostream& operator<<(long double __f);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:245:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'const void *' for 1st argument; take the address of the argument with &  
    basic_ostream& operator<<(const void* __p);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:254:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'basic_streambuf<char_type, traits_type> *' (aka 'basic_streambuf<char, std::char_traits<char>> *') for 1st argument  
    basic_ostream& operator<<(basic_streambuf<char_type, traits_type>* __sb);  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/ostream:260:20: note: candidate function not viable: no known conversion from 'histogram<decltype(a), S>' (aka 'histogram<tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default>>, boost::histogram::unlimited_storage<std::allocator<char>>>') to 'nullptr_t' (aka 'std::nullptr_t') for 1st argument  
    basic_ostream& operator<<(nullptr_t)  
                   ^  
1 error generated.  
make[2]: *** [examples/CMakeFiles/example-hello_world.dir/example-hello_world.cxx.o] Error 1  
make[1]: *** [examples/CMakeFiles/example-hello_world.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 1

> Username: henryiii  
> Created at: 2024-03-30 05:22:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/396#issuecomment-2027922964  

You are missing:  
  
```cpp  
#include <boost/histogram/ostream.hpp>  
```

---

## Comment 2

> Username: taehyounpark  
> Created at: 2024-03-30 05:25:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/396#issuecomment-2027923690  

Indeed, apologies for my silly mistake and thank you for pointing it out :slightly_smiling_face:
