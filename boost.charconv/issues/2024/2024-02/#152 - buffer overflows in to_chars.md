# #152 - buffer overflows in to_chars [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-02 08:49:43 UTC  
> Updated at: 2024-02-07 12:10:17 UTC  
> Closed at: 2024-02-07 12:10:17 UTC  
> Url: https://github.com/boostorg/charconv/issues/152  

Hello Matt,  
here is a somewhat clearer test case.  
[test.cpp.txt](https://github.com/cppalliance/charconv/files/14137536/test.cpp.txt)  
  
FP128  
No buffer overflow, but the values in to_chars_result do not match the default.  
  
all others  
buffer overflows + wrong to_chars_result  
  
thx  
Gero

---

## Comment 1

> Username: gpeterhoff  
> Created at: 2024-02-02 16:37:49 UTC  
> Updated at: 2024-02-03 06:55:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/152#issuecomment-1924240160  

Hello Matt,  
as I already wrote in my mail, it makes sense to differentiate the finite/nonfinite cases. This has the advantage that in the actual implementations for finite the nonfinite cases no longer have to be considered.   
These implementations should be designed in such a way that to_chars_result is passed as a reference. This means that the values only need to be set in the event of success.  
  
By using boost::string_view and std::copy, various pointers no longer have to be calculated manually and values copied manually via std::memcpy, which is **highly error-prone**. In addition, std::copy has been constexpr since C++20, which is not the case with std::memcpy.  
  
Overall, this makes the code much clearer and more error-resistant.  
```  
namespace boost  
{  
namespace charconv  
{  
namespace detail  
{  
#define BOOST_CHARCONV_COMPATIBLE  
  
static constexpr boost::string_view  
	inf_pos{"+inf"},  
	inf_neg{"-inf"},  
#if defined(BOOST_CHARCONV_COMPATIBLE)  
	qnan_pos{"nan"},  
	qnan_neg{"-nan"},  
	snan_pos{"nan"},  
	snan_neg{"-nan"};  
#else  
	qnan_pos{"nan"},		//	"+nan(qnan)" "+qnan"  
	qnan_neg{"-nan(ind)"},	//	"-nan(qnan)" "-qnan"  
	snan_pos{"nan(snan)"},	//	"+nan(snan)" "+snan"  
	snan_neg{"-nan(snan)"};	//	"-nan(snan)" "-snan"  
#endif  
  
  
  
inline constexpr bool	is_valid_range(const char*const first, const char*const last)	noexcept  
{  
	return (first!=nullptr && last!=nullptr) ? first < last : false;  
}  
  
inline /*BOOST_CXX20_CONSTEXPR*/ void to_chars_nonfinite_copystring(to_chars_result& result, char*const first, const size_t size, const boost::string_view& value) noexcept  
{  
	if (BOOST_LIKELY(value.size() <= size))  
	{  
		std::copy(value.data(), value.data()+value.size(), first);  
		result = {first + value.size(), std::errc{}};  
	}  
}  
  
template <typename Type>  
inline /*BOOST_CXX23_CONSTEXPR*/ typename std::enable_if<std::is_floating_point<Type>::value, void>::type  
to_chars_nonfinite(to_chars_result& result, char*const first, const size_t size, const Type value) noexcept  
{  
	const bool  
		is_negativ = std::signbit(value);  
  
	if (std::isinf(value))  
	{  
		to_chars_nonfinite_copystring(result, first, size, is_negativ ? inf_neg : inf_pos);  
	}  
	//	replace __builtin_issignaling in the real implementation  
	else if (__builtin_issignaling(value))  
	{  
		to_chars_nonfinite_copystring(result, first, size, is_negativ ? snan_neg : snan_pos);  
	}  
	else if (std::isnan(value))  
	{  
		to_chars_nonfinite_copystring(result, first, size, is_negativ ? qnan_neg : qnan_pos);  
	}  
}  
  
template <typename Type>  
inline typename std::enable_if<std::is_floating_point<Type>::value, to_chars_result>::type  
to_chars_float_impl_new(char*const first, char*const last, const Type value, const chars_format format = chars_format::general, const int precision = -1) noexcept  
{  
	to_chars_result  
		result{last, std::errc::value_too_large};  
  
	if (BOOST_LIKELY(is_valid_range(first, last)))  
	{  
		//	size now is guaranteed positive  
		const size_t  
			size = size_t(last - first);  
  
		if (BOOST_LIKELY(std::isfinite(value)))  
		{  
			//	never contains nan/inf  
  
			//	so that it works at all  
			result = boost::charconv::to_chars(first, last, value, format, precision);  
  
			//	would be correct  
			//	to_chars_finite(result, first, size, value, format, precision);  
		}  
		else  
		{  
			to_chars_nonfinite(result, first, size, value);  
		}  
	}  
	return result;  
}  
  
}	//	detail  
}	//	charconv  
}	//	boost  
```  
  
Adjustments in the namespace test  
```  
inline void show_result(const boost::string_view& info, const buffer_type& buffer, const std::to_chars_result& result, const std::chars_format format)  
{  
	const std::string  
		message = std::make_error_code(result.ec).message();  
	const ptrdiff_t  
		size = ptrdiff_t(result.ptr - buffer.data());  
  
	std::cout << "\t(" << info << ", " << to_string(format) << ")\t";  
	if (size >= 0)  
	{  
		if (size_t(size) <= buffer.size())  
		{  
			if (result.ec == std::errc{})	std::cout << boost::string_view{buffer.data(), size_t(size)};  
			else							std::cout << "{}";  
			std::cout << "\t(" << size << ", " << message << ")\n";  
		}  
		else  
		{  
			//	buffer overflow  
			std::cout << "BOF!\t(" << size << ", " << message << ")\n";  
		}  
	}  
	else  
	{  
		//	invalid result.ptr  
		//	currently no known case  
		std::cout << "PTR!\t(" << size << ", " << message << ")\n";  
	}  
}  
  
template <typename Type>  
inline typename std::enable_if<std::is_floating_point<Type>::value, void>::type  
show_format(buffer_type& buffer, const Type value, const std::chars_format format)  
{  
	std::to_chars_result  
		result;  
  
	buffer.fill(0);  
	result = std::to_chars(buffer.data(), buffer.data()+buffer.size(), value, format);  
	show_result("std  ", buffer, result, format);  
  
	buffer.fill(0);  
	result = to_result(boost::charconv::detail::to_chars_float_impl_new(buffer.data(), buffer.data()+buffer.size(), value, to_format(format)));  
	show_result("boost", buffer, result, format);  
	std::cout << '\n';  
}  
```  
  
regards  
Gero
