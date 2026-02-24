# #267 - wrong result generated from boost::charconv::to_chars with precision [Closed]

> Username: wanghan02  
> Created at: 2025-06-16 13:28:06 UTC  
> Updated at: 2025-06-16 20:07:27 UTC  
> Closed at: 2025-06-16 20:07:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/267  

Example code could be found as below. `boost::charconv::to_chars` gives `0.01` instead of `0.10` when called with `0.099`, `char_formats::fixed` and `precision(2)`. I'm quite shocked that simple cases like this could go wrong which makes boost::charconv not usable in this stage. It would be great if the unit test coverage could be expanded so that simple cases like this could be found by unit tests not the user. Thanks!  
  
```  
#include <boost/charconv.hpp>  
#include <cassert>  
#include <string>  
#include <iostream>  
#include <charconv>  
  
template<typename Func, typename T, typename TFormat>  
std::string ToCharsPrecision(Func func, T const t, TFormat const format, int const nPrecision) {  
	std::array<char, 100> buf;  
	auto const result = func(buf.data(), buf.data() + buf.size(), t, format, nPrecision);  
	assert(result.ec == std::errc());  
	return std::string(buf.data(), result.ptr);  
}  
  
template<typename T>  
std::string ToCharsPrecisionStd(T const t, int const nPrecision) {  
	return ToCharsPrecision([](auto... args) { return std::to_chars(args...); }, t, std::chars_format::fixed, nPrecision);  
}  
  
template<typename T>  
std::string ToCharsPrecisionBoost(T const t, int const nPrecision) {  
	return ToCharsPrecision([](auto... args) { return boost::charconv::to_chars(args...); }, t, boost::charconv::chars_format::fixed, nPrecision);  
}  
  
template<typename T>  
void ToCharsPrecisionCompare(T const t, int const nPrecision) {  
	std::cout << "--------------------------------" << std::endl;  
	std::cout << "number : " << t << std::endl;  
	std::cout << "std    : " << ToCharsPrecisionStd(t, nPrecision) << std::endl;  
	std::cout << "boost  : " << ToCharsPrecisionBoost(t, nPrecision) << std::endl;  
}  
  
int main() {  
	ToCharsPrecisionCompare(0.09, 2);        // expect 0.09  
	ToCharsPrecisionCompare(0.099, 2);       // expect 0.10  
	ToCharsPrecisionCompare(0.0999, 2);      // expect 0.10  
	ToCharsPrecisionCompare(0.09999, 2);     // expect 0.10  
	ToCharsPrecisionCompare(0.099999, 2);    // expect 0.10  
	ToCharsPrecisionCompare(0.0999999, 2);   // expect 0.10  
	ToCharsPrecisionCompare(0.09999999, 2);  // expect 0.10  
	return 0;  
}  
```  
Output:  
  
```  
--------------------------------  
number : 0.09  
std    : 0.09  
boost  : 0.09  
--------------------------------  
number : 0.099  
std    : 0.10  
boost  : 0.01  
--------------------------------  
number : 0.0999  
std    : 0.10  
boost  : 0.01  
--------------------------------  
number : 0.09999  
std    : 0.10  
boost  : 0.01  
--------------------------------  
number : 0.099999  
std    : 0.10  
boost  : 0.01  
--------------------------------  
number : 0.0999999  
std    : 0.10  
boost  : 0.01  
--------------------------------  
number : 0.1  
std    : 0.10  
boost  : 0.01  
```
