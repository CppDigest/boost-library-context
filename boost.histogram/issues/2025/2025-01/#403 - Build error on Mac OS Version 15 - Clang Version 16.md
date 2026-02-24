# #403 - Build error on Mac OS Version 15 - Clang Version 16 [Open]

> Username: colintoal  
> Created at: 2025-01-14 17:05:28 UTC  
> Updated at: 2025-01-14 17:05:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/403  

Building a project with Boost Histogram on Mac OS 15 with:  
  
Apple clang version 16.0.0 (clang-1600.0.26.6)  
Target: arm64-apple-darwin24.3.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
  
Using the following command line:  
  
g++ -std=c++14 -DHAVE_CONFIG_H -I. -I../../include -DBOOST_ALL_DYN_LINK -I/opt/local/include -I/usr/local/include -I/opt/local/include -I/usr/local/include -I/opt/local/include -I/usr/local/include -I/usr/local/include -g -O2 -MT test.lo -MD -MP -MF .deps/test.Tpo -c test.cpp  -fno-common -DPIC -o .libs/Image  
  
Creates the following error:  
In file included from test.cpp:4:  
In file included from /usr/local/include/boost/histogram.hpp:27:  
In file included from /usr/local/include/boost/histogram/algorithm.hpp:15:  
In file included from /usr/local/include/boost/histogram/algorithm/empty.hpp:11:  
In file included from /usr/local/include/boost/histogram/indexed.hpp:16:  
/usr/local/include/boost/histogram/detail/operators.hpp:48:14: error: use of overloaded operator '>' is ambiguous (with operand types 'const boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>>>::accessor' and 'const boost::histogram::unlimited_storage<>::reference')  
   48 |     return b > a;  
      |            ~ ^ ~  
/usr/local/include/boost/histogram/indexed.hpp:191:20: note: in instantiation of function template specialization 'boost::histogram::detail::operator<<boost::histogram::unlimited_storage<>::reference>' requested here  
  191 |       return get() < o;  
      |                    ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/c++/v1/__algorithm/comp.h:41:18: note: in instantiation of function template specialization 'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>>>::accessor::operator<<boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>>>::accessor>' requested here  
   41 |     return __lhs < __rhs;  
      |                  ^  
/usr/local/include/boost/histogram/detail/operators.hpp:51:28: note: candidate function [with U = boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>>>::accessor]  
   51 |   friend if_not_same<T, U> operator>(const U& a, const T& b) noexcept {  
      |                            ^  
/usr/local/include/boost/histogram/indexed.hpp:195:10: note: candidate function [with U = boost::histogram::unlimited_storage<>::reference]  
  195 |     bool operator>(const U& o) const noexcept {  
      |          ^  
1 error generated.  
  
g++ aliases to clang - exact same result occurs with clang as the command.
