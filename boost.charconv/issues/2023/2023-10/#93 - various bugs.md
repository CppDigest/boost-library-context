# #93 - various bugs [Closed]

> Username: gpeterhoff  
> Created at: 2023-10-25 18:14:18 UTC  
> Updated at: 2023-10-27 12:35:51 UTC  
> Closed at: 2023-10-27 07:07:45 UTC  
> Url: https://github.com/boostorg/charconv/issues/93  

Hello Matt,  
**to_chars for std::bfloat16_t is not available**  
  
You define BOOST_CHARCONV_HAS_BRAINFLOAT16 in config.hpp, but check for BOOST_CHARCONV_HAS_BFLOAT16 in to_chars.hpp (and for BOOST_CHARCONV_HAS_BRAINFLOAT16 in from_chars.hpp).  
Why these error-prone redundancies?  
  
**to_chars for FP-Types not work**  
  
./Test -nan  
gets always ```-nan(ind)```  
  
./Test 3.3   
gets  
```  
__float128:  
std	3.300000	3.300000  
boost	3.3000	3.3000  
  
long double:  
std	3.300000	3.300000  
boost	3.3000	3.3000  
  
double:  
std	3.300000	3.300000  
boost	3.300000e+00	3.300000e+00  
  
float:  
std	3.300000	3.300000  
boost	3.300000e+00	3.300000e+00  
  
_Float128:  
std	3.300000	3.300000  
boost	3.3000	3.3000  
  
_Float64:  
std	3.300000	3.300000  
boost	3.300000e+00	3.300000e+00  
  
_Float32:  
std	3.300000	3.300000  
boost	3.300000e+00	3.300000e+00  
  
_Float16:  
std	3.300781	3.300781  
boost	3.300781e+00	3.300781e+00  
  
std::bfloat16_t:  
std	3.296875	3.296875  
boost	3.296875e+00	3.296875e+00  
```  
  
Also, to_chars_result.ptr does not always match the actual written values (buffer overflow, hence the double output in print), e.g. compiled with fmt=general:  
```  
__float128:  
std	3.3	3.3  
boost	3.3	3.3  
  
long double:  
std	3.3	3.3  
boost	3.3  
	3.3  
  
  
double:  
std	3.3	3.3  
boost	3.3e+00	3.3e+000  
  
float:  
std	3.3	3.3  
boost	3.3e+00	3.3e+000  
  
_Float128:  
std	3.3	3.3  
boost	3.3	3.3  
  
_Float64:  
std	3.3	3.3  
boost	3.3e+00	3.3e+000  
  
_Float32:  
std	3.3	3.3  
boost	3.3e+00	3.3e+000  
  
_Float16:  
std	3.30078	3.30078  
boost	3.300781e+00	3.300781e+00  
  
std::bfloat16_t:  
std	3.29688	3.29688  
boost	3.296875e+00	3.296875e+00  
```  
  
code  
```  
namespace test  
{  
using buffer_type = std::array<char, 1024>;  
  
inline constexpr boost::charconv::chars_format	to_format(const std::chars_format format)	noexcept  
{  
	switch (format)  
	{  
		case std::chars_format::scientific:	return boost::charconv::chars_format::scientific;  
		case std::chars_format::fixed:		return boost::charconv::chars_format::fixed;  
		case std::chars_format::hex:		return boost::charconv::chars_format::hex;  
		default:				return boost::charconv::chars_format::general;  
	}  
}  
  
template <typename Type>  
Type get_as(const int argc, const char*const*const args)	noexcept  
{  
	Type result{};  
	if (argc > 1)	[[likely]]  
	{  
		const std::string_view view(args[1]);  
		std::from_chars(view.data(), view.data()+view.size(), result);  
	}  
	return result;  
}  
  
template <typename Type>  
inline std::string nameof()  
{  
	return boost::core::demangle(typeid(Type).name());  
}  
  
template <typename Result>  
void print(const Result& res, const buffer_type& buf)  
{  
	if constexpr (std::is_same_v<Result, std::to_chars_result>)	std::cout << "std\t";  
	else								std::cout << "boost\t";  
  
	if (res.ec == std::errc{})	[[likely]]	std::cout << std::string_view(buf.data(), res.ptr) << '\t' << buf.data() << std::endl;  
	else						std::cout << "error\n";  
}  
  
template <std::floating_point Type>  
void test_to_chars(const Type value, const std::chars_format format, const int precision)  
{  
	buffer_type buffer{};  
	std::cout << nameof<Type>() << ":\n";  
	const auto rs = std::to_chars(buffer.data(), buffer.data()+buffer.size(), value, format, precision);  
	print(rs, buffer);  
	buffer.fill(char{});  
	const auto rb = boost::charconv::to_chars(buffer.data(), buffer.data()+buffer.size(), value, to_format(format), precision);  
	print(rb, buffer);  
	std::cout << std::endl;  
}  
  
}	//	test  
  
int main(const int argc, const char*const*const args)  
{  
	using namespace test;  
	constexpr std::chars_format fmt{std::chars_format::fixed};  
	constexpr int pre{6};  
  
	test_to_chars(get_as<boost::float128_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<boost::float80_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<boost::float64_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<boost::float32_t>(argc, args), fmt, pre);  
  
	test_to_chars(get_as<std::float128_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<std::float64_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<std::float32_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<std::float16_t>(argc, args), fmt, pre);  
	test_to_chars(get_as<std::bfloat16_t>(argc, args), fmt, pre);  
  
	return EXIT_SUCCESS;  
}  
```  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-10-26 06:58:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/93#issuecomment-1780523779  

> Hello Matt, **to_chars for std::bfloat16_t is not available**  
>   
> You define BOOST_CHARCONV_HAS_BRAINFLOAT16 in config.hpp, but check for BOOST_CHARCONV_HAS_BFLOAT16 in to_chars.hpp (and for BOOST_CHARCONV_HAS_BRAINFLOAT16 in from_chars.hpp). Why these error-prone redundancies?  
>   
  
Fixed consistency. I had messed up FLOAT16 and BFLOAT16 once so I changed the name entirely to make it more distinguishable.   
  
> **to_chars for FP-Types not work**  
>   
> ./Test -nan gets always `-nan(ind)`  
>   
  
This is the expected value per the docs. Clang 16+ and MSVC return this value, but since I assume you're running GCC 13 +to use `<stdfloat>` for these test it will not.  
  
> ./Test 3.3 gets  
>   
  
Fixed.  
  
PR will be published shortly once the other is merged.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-10-27 12:35:51 UTC  
> Url: https://github.com/boostorg/charconv/issues/93#issuecomment-1782842861  

>> ./Test -nan gets always -nan(ind)  
  
> This is the expected value per the docs. Clang 16+ and MSVC return this value, but since I assume you're running GCC 13 +to use <stdfloat> for these test it will not.  
  
??? Is this perhaps an MSVC "specialty"? Both intel and clang give -nan; godbolt unfortunately has no executor for MSVC so I can't check.  
https://godbolt.org/z/r7fMf9bzj
