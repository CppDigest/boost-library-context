# #266 - to_chars does not generate correct number of decimal places when the precision is 5 for large numbers [Closed]

> Username: wanghan02  
> Created at: 2025-06-12 13:52:37 UTC  
> Updated at: 2025-06-16 21:14:25 UTC  
> Closed at: 2025-06-16 21:14:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/266  

Example code could be found below.  
  
```  
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
	std::cout<<"--------------------------------"<<std::endl;  
	std::cout<<"std  : "<<ToCharsPrecisionStd(t, nPrecision)<<std::endl;  
	std::cout<<"boost: "<<ToCharsPrecisionBoost(t, nPrecision)<<std::endl;  
}  
  
int main() {  
	std::cout<<std::endl;  
	ToCharsPrecisionCompare(123456789012345.6789012345678901, 5);  
	ToCharsPrecisionCompare(1234567890123456.789012345678901, 5);  
	ToCharsPrecisionCompare(12345678901234567.89012345678901, 5);  
	ToCharsPrecisionCompare(123456789012345678.9012345678901, 5);  
	ToCharsPrecisionCompare(1234567890123456789.012345678901, 5);  
	ToCharsPrecisionCompare(12345678901234567890.12345678901, 5);  
	ToCharsPrecisionCompare(123456789012345678901.2345678901, 5);  
	ToCharsPrecisionCompare(12345678901234567890123.45678901, 5);  
	ToCharsPrecisionCompare(123456789012345678901234.5678901, 5);  
	ToCharsPrecisionCompare(1234567890123456789012345.678901, 5);  
	ToCharsPrecisionCompare(12345678901234567890123456.78901, 5);  
	ToCharsPrecisionCompare(123456789012345678901234567.8901, 5);  
}  
```  
  
The output is with boost 1.87.0:  
```  
--------------------------------  
std  : 123456789012345.67188  
boost: 123456789012345.67188  
--------------------------------  
std  : 1234567890123456.75000  
boost: 1234567890123456.75000  
--------------------------------  
std  : 12345678901234568.00000  
boost: 12345678901234568.00000  
--------------------------------  
std  : 123456789012345680.00000  
boost: 123456789012345680.00000  
--------------------------------  
std  : 1234567890123456768.00000  
boost: 1234567890123456768.00000  
--------------------------------  
std  : 12345678901234567168.00000  
boost: 12345678901234567168.00000  
--------------------------------  
std  : 123456789012345683968.00000  
boost: 123456789012345683968.000  
--------------------------------  
std  : 12345678901234567741440.00000  
boost: 12345678901234567741440.000  
--------------------------------  
std  : 123456789012345685803008.00000  
boost: 123456789012345685803008.000  
--------------------------------  
std  : 1234567890123456824475648.00000  
boost: 1234567890123456824475648.000  
--------------------------------  
std  : 12345678901234568244756480.00000  
boost: 12345678901234568244756480.000  
--------------------------------  
std  : 123456789012345678152597504.00000  
boost: 123456789012345678152597504.000  
```  
  
Last 6 cases only generate 3 decimal places instead of 5.
