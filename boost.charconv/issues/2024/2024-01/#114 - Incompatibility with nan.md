# #114 - Incompatibility with nan [Closed]

> Username: gpeterhoff  
> Created at: 2024-01-05 20:00:41 UTC  
> Updated at: 2024-05-07 13:29:19 UTC  
> Closed at: 2024-01-09 07:07:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/114  

Hi Matt,  
first the code:  
```  
template <typename Type>  
void	from_and_show(const boost::string_view& view)  
{  
	Type  
		value{};  
  
	{  
		const boost::charconv::from_chars_result  
			result = boost::charconv::from_chars(view.begin(), view.end(), value);  
  
		std::cout << "boost:";  
		if (result.ec == std::errc{})  
		{  
			std::cout << value;  
		}  
		else  
		{  
			std::cout << std::make_error_code(result.ec).message();  
		}  
	}  
	std::cout << '\t';  
  
	//	std::from_chars not support __float128  
	BOOST_IF_CONSTEXPR (!std::is_same<Type, boost::float128_type>::value)  
	{  
		const std::from_chars_result  
			result = std::from_chars(view.begin(), view.end(), value);  
  
		std::cout << "std:";  
		if (result.ec == std::errc{})  
		{  
			std::cout << value;  
		}  
		else  
		{  
			std::cout << std::make_error_code(result.ec).message();  
		}  
	}  
	std::cout << '\n';  
}  
  
template <typename Type>  
void	to_and_show(const Type value)  
{  
	std::array<char, 32>  
		string;  
  
	{  
		string.fill(0);  
		const boost::charconv::to_chars_result  
			result = boost::charconv::to_chars(string.begin(), string.end(), value);  
  
		std::cout << "boost:";  
		if (result.ec == std::errc{})  
		{  
			std::cout << string.data();  
		}  
		else  
		{  
			std::cout << std::make_error_code(result.ec).message();  
		}  
	}  
	std::cout << '\t';  
  
	//	std::to_chars not support __float128  
	BOOST_IF_CONSTEXPR (!std::is_same<Type, boost::float128_type>::value)  
	{  
		string.fill(0);  
		const std::to_chars_result  
			result = std::to_chars(string.begin(), string.end(), value);  
  
		std::cout << "std:";  
		if (result.ec == std::errc{})  
		{  
			std::cout << string.data();  
		}  
		else  
		{  
			std::cout << std::make_error_code(result.ec).message();  
		}  
	}  
	std::cout << '\n';  
}  
  
int main()  
{  
	const boost::string_view  
		nan_pos{"+nan"},  
		nan_neg{"-nan"},  
		nan{"nan"};  
  
	std::cout << "from nan_pos:\n";  
	from_and_show<boost::float128_type>(nan_pos);  
	from_and_show<boost::float80_t>(nan_pos);  
	from_and_show<boost::float64_t>(nan_pos);  
	from_and_show<boost::float32_t>(nan_pos);  
  
	std::cout << "\nfrom nan_neg:\n";  
	from_and_show<boost::float128_type>(nan_neg);  
	from_and_show<boost::float80_t>(nan_neg);  
	from_and_show<boost::float64_t>(nan_neg);  
	from_and_show<boost::float32_t>(nan_neg);  
  
	std::cout << "\nfrom nan:\n";  
	from_and_show<boost::float128_type>(nan);  
	from_and_show<boost::float80_t>(nan);  
	from_and_show<boost::float64_t>(nan);  
	from_and_show<boost::float32_t>(nan);  
  
	std::cout << "\nto qnan:\n";  
	to_and_show(std::numeric_limits<boost::float128_type>::quiet_NaN());  
	to_and_show(std::numeric_limits<boost::float80_t>::quiet_NaN());  
	to_and_show(std::numeric_limits<boost::float64_t>::quiet_NaN());  
	to_and_show(std::numeric_limits<boost::float32_t>::quiet_NaN());  
  
	std::cout << "\nto -qnan:\n";  
	to_and_show(-std::numeric_limits<boost::float128_type>::quiet_NaN());  
	to_and_show(-std::numeric_limits<boost::float80_t>::quiet_NaN());  
	to_and_show(-std::numeric_limits<boost::float64_t>::quiet_NaN());  
	to_and_show(-std::numeric_limits<boost::float32_t>::quiet_NaN());  
  
	std::cout << "\nto snan:\n";  
	to_and_show(std::numeric_limits<boost::float128_type>::signaling_NaN());  
	to_and_show(std::numeric_limits<boost::float80_t>::signaling_NaN());  
	to_and_show(std::numeric_limits<boost::float64_t>::signaling_NaN());  
	to_and_show(std::numeric_limits<boost::float32_t>::signaling_NaN());  
  
	std::cout << "\nto -snan:\n";  
	to_and_show(-std::numeric_limits<boost::float128_type>::signaling_NaN());  
	to_and_show(-std::numeric_limits<boost::float80_t>::signaling_NaN());  
	to_and_show(-std::numeric_limits<boost::float64_t>::signaling_NaN());  
	to_and_show(-std::numeric_limits<boost::float32_t>::signaling_NaN());  
  
	return EXIT_SUCCESS;  
}  
```  
  
and the results (gcc):  
from nan_pos:  
boost:Invalid argument	  
boost:Invalid argument	std:Invalid argument  
boost:Invalid argument	std:Invalid argument  
boost:Invalid argument	std:Invalid argument  
  
from nan_neg:  
boost:Invalid argument	  
boost:Invalid argument	std:-nan  
boost:-nan	std:-nan  
boost:-nan	std:-nan  
  
from nan:  
boost:Invalid argument	  
boost:Invalid argument	std:nan  
boost:nan	std:nan  
boost:nan	std:nan  
  
to qnan:  
boost:nan	  
boost:nan	std:nan  
boost:nan	std:nan  
boost:nan	std:nan  
  
to -qnan:  
boost:-nan(ind)	  
boost:-nan(ind)	std:-nan  
boost:-nan(ind)	std:-nan  
boost:-nan(ind)	std:-nan  
  
to snan:  
boost:nan(snan)	  
boost:nan(snan)	std:nan  
boost:nan(snan)	std:nan  
boost:nan(snan)	std:nan  
  
to -snan:  
boost:-nan(snan)	  
boost:-nan(snan)	std:-nan  
boost:-nan(snan)	std:-nan  
boost:-nan(snan)	std:-nan  
  
in general:  
from_chars("+nan") is also not supported by the standard (unfortunately).  
  
string values for nan:  
These deviate from the standard ("nan"/"-nan"). This is documented https://develop.charconv.cpp.al/#to_chars_to_chars_for_floating_point_types, but boost::charconv is incompatible with it.  
  
from_chars "nan"/"-nan":  
boost::float128_type and boost::float80_t -> do not work  
boost::float64_t and boost::float32_t -> work  
  
to_chars:  
-numeric_limits::quiet_NaN and ±numeric_limits::signaling_NaN -> do not work (see string values)  
(+)numeric_limits::quiet_NaN -> works  
  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2024-01-08 05:59:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/114#issuecomment-1880425466  

I will take a look. `boost::float80_t` and `float128_t` use a different path in `from_chars` than 32 and 64 do so at least it's consistently wrong.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-05-07 13:29:18 UTC  
> Url: https://github.com/boostorg/charconv/issues/114#issuecomment-2098413367  

Hi Matt,  
I mean this:  
```  
template <typename Type>  
void	println_float(const Type value)  
{  
	std::array<char, 256>  
		arr_s{}, arr_b{};  
	const std::to_chars_result  
		res_s = std::to_chars(arr_s.data(), arr_s.data()+arr_s.size(), value, std::chars_format::general);  
	const boost::charconv::to_chars_result  
		res_b = boost::charconv::to_chars(arr_b.data(), arr_b.data()+arr_b.size(), value, boost::charconv::chars_format::general);  
  
	std::cout << "stream           " << value << std::endl;  
	std::cout << "to_chars std/gcc " << std::string_view{arr_s.data(), size_t(res_s.ptr-arr_s.data())} << std::endl;  
	std::cout << "to_chars boost   " << std::string_view{arr_b.data(), size_t(res_b.ptr-arr_b.data())} << std::endl;  
	std::cout << std::endl;  
}  
  
int main()  
{  
	using T = double;  
	using L = std::numeric_limits<T>;  
  
	println_float( L::quiet_NaN());  
	println_float(-L::quiet_NaN());  
	println_float( L::signaling_NaN());  
	println_float(-L::signaling_NaN());  
  
	return EXIT_SUCCESS;  
}  
```  
Results  
```  
stream           nan  
to_chars std/gcc nan  
to_chars boost   nan  
  
stream           -nan  
to_chars std/gcc -nan  
to_chars boost   -nan(ind)  
  
stream           nan  
to_chars std/gcc nan  
to_chars boost   nan(snan)  
  
stream           -nan  
to_chars std/gcc -nan  
to_chars boost   -nan(snan)  
```  
regards  
Gero
