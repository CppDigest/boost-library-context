# #195 - charconv2 [Open]

> Username: gpeterhoff  
> Created at: 2024-05-07 02:19:32 UTC  
> Updated at: 2024-05-17 06:48:03 UTC  
> Url: https://github.com/boostorg/charconv/issues/195  

Hello Matt,  
I would like to provide an extended library boost::charconv2. This implements:  
- FP types: an additional parameter nonfinite_style which means whether to distinguish between quiet_NaN and signaling_NaN. The default is no distinction - which ensures compatibility with std::from/to_chars. Unfortunately, your boost::charchonv does not behave according to the standard, which is why this measure is necessary.  
- additional overloads for from_chars: it is highly stupid that there are 2 different implementations for string_view in boost (boost::core::string_view and boost::string_view).  
- from/to_chars_binary  
  
Implementation  
I need a few more things:  
- is_floating_point_same [is_floating_point_same.hpp.txt](https://github.com/boostorg/charconv/files/15228827/is_floating_point_same.hpp.txt)  
- math_limits [math_limits.hpp.txt](https://github.com/boostorg/charconv/files/15228781/math_limits.hpp.txt)  
- is_integer [is_integer.hpp.txt](https://github.com/boostorg/charconv/files/15228519/is_integer.hpp.txt)  
- is_number [is_number.hpp.txt](https://github.com/boostorg/charconv/files/15228520/is_number.hpp.txt)  
- endian [endian.hpp.txt](https://github.com/boostorg/charconv/files/15228526/endian.hpp.txt)  
- std::issignaling  
  
[charconv2.hpp.txt](https://github.com/boostorg/charconv/files/15228527/charconv2.hpp.txt)  
  
  
Examples  
```  
namespace example  
{  
namespace C2 = boost::charconv2;  
using array_type = std::array<char, 256>;  
  
template <typename Array, typename Result>  
void do_println(const Array& arr, const Result& res)  
{  
	if (res.ec == std::errc{})  
	{  
		std::cout << std::string_view{arr.data(), size_t(res.ptr-arr.data())};  
	}  
	else  
	{  
		std::cout << std::make_error_code(res.ec).message();  
	}  
	std::cout << std::endl;  
}  
  
template <typename Type>  
typename std::enable_if<boost::is_integer<Type>::value, void>::type	println(const Type value, const int base = 10)  
{  
	array_type  
		arr{};  
	const C2::to_chars_result  
		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, base);  
	do_println(arr, res);  
}  
  
template <typename Type>  
typename std::enable_if<boost::is_floating_point<Type>::value, void>::type	println(const Type value, const C2::chars_format format, const int precision, const C2::nonfinite_style style = C2::nonfinite_style::standard)  
{  
	array_type  
		arr{};  
	const C2::to_chars_result  
		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, format, precision, style);  
	do_println(arr, res);  
}  
  
template <typename Type>  
typename std::enable_if<boost::is_floating_point<Type>::value, void>::type	println(const Type value, const C2::chars_format format, const C2::nonfinite_style style = C2::nonfinite_style::standard)  
{  
	array_type  
		arr{};  
	const C2::to_chars_result  
		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, format, style);  
	do_println(arr, res);  
}  
  
  
template <typename Type>  
typename std::enable_if<boost::is_number<Type>::value, void>::type	println_binary(const Type value, const C2::binary_style style = C2::binary_style::standard)  
{  
	array_type  
		arr{};  
	const C2::to_chars_result  
		res = C2::to_chars_binary(arr.data(), arr.data()+arr.size(), value, style);  
	do_println(arr, res);  
}  
  
}	//	example  
  
int main()  
{  
	namespace C2 = boost::charconv2;  
	using F = boost::float64_t;  
	using I = int;  
  
	I i = 42;  
	example::println(i);  
	example::println_binary(i);  
	example::println_binary(i, C2::binary_style::showpoint);  
	std::cout << std::endl;  
  
	F f = 42;  
	example::println(f, C2::chars_format::general);  
	example::println(f, C2::chars_format::general, C2::nonfinite_style::explicite_nan);  
	example::println_binary(f);  
	example::println_binary(f, C2::binary_style::showpoint);  
	std::cout << std::endl;  
  
	f = std::numeric_limits<F>::signaling_NaN();  
	example::println(f, C2::chars_format::general);  
	example::println(f, C2::chars_format::general, C2::nonfinite_style::explicite_nan);  
	example::println_binary(f);  
	example::println_binary(f, C2::binary_style::showpoint);  
	std::cout << std::endl;  
  
	return EXIT_SUCCESS;  
}  
```  
Results  
```  
42  
00000000000000000000000000101010  
00000000.00000000.00000000.00101010  
  
42  
42  
0100000001000101000000000000000000000000000000000000000000000000  
0.10000000100.0101000000000000000000000000000000000000000000000000  
  
nan  
snan  
0111111111110100000000000000000000000000000000000000000000000000  
0.11111111111.0100000000000000000000000000000000000000000000000000  
```  
  
What is your opinion on this? I would be delighted to receive your comments.  
  
regards  
Gero  
  
Edit  
I just realized that this will not work for you. This is because I have rewritten boost::math::cstdfloat -> into a separate library boost::cstdfloat. Why?  
- boost::math::cstdfloat contains many errors  
- boost::math::cstdfloat is implemented in a very complicated and outdated style; nobody can understand it anymore -> unmaintainable  
  
Here for example my types.hpp vs. boost::math::cstdfloat::cstdfloat_types.hpp [types.hpp.txt](https://github.com/boostorg/charconv/files/15229723/types.hpp.txt)

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-07 06:55:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2097579920  

> Hello Matt, I would like to provide an extended library boost::charconv2. This implements:  
>   
> * FP types: an additional parameter nonfinite_style which means whether to distinguish between quiet_NaN and signaling_NaN. The default is no distinction - which ensures compatibility with std::from/to_chars. Unfortunately, your boost::charchonv does not behave according to the standard, which is why this measure is necessary.  
  
This is not the first time you have made this claim (see: https://github.com/boostorg/math/issues/1118) without any backing. I ask you please:  
1) Quote a valid piece of the standard that says I am incorrect  
2) Open a valid bug report  
3) Stop making this claim  
  
As far as I am concerned the standard says there is only implementation specific compatibility for roundtripping values, and I know that qNaNs and sNaNs are distinguished and roundtripped correctly here. Quote from section 22.13.2 Note 1 `[Note 1 : This guarantee applies only if to_chars and from_chars are executed on the same implementation. — end note ]`  
  
> * additional overloads for from_chars: it is highly stupid that there are 2 different implementations for string_view in boost (boost::core::string_view and boost::string_view).  
  
These are already in the library: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/from_chars.hpp#L226. I agree it's not an ideal situation but this overload takes `std::string`, `boost::core::string_view`, `boost::string_view`, or `std::string_view`.  
  
> * from/to_chars_binary  
  
For floating point types? This can also be trivially implemented on the user end by `std::memcpy`/`std::bit_cast` into an integer and then using base = 2.  
  
>   
> Implementation I need a few more things:  
>   
> * is_floating_point_same [is_floating_point_same.hpp.txt](https://github.com/boostorg/charconv/files/15228827/is_floating_point_same.hpp.txt)  
> * math_limits [math_limits.hpp.txt](https://github.com/boostorg/charconv/files/15228781/math_limits.hpp.txt)  
> * is_integer [is_integer.hpp.txt](https://github.com/boostorg/charconv/files/15228519/is_integer.hpp.txt)  
> * is_number [is_number.hpp.txt](https://github.com/boostorg/charconv/files/15228520/is_number.hpp.txt)  
> * endian [endian.hpp.txt](https://github.com/boostorg/charconv/files/15228526/endian.hpp.txt)  
> * std::issignaling  
>   
  
`std::issignaling` does not exist. You'll find a few workaround implementations here: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/issignaling.hpp  
  
> [charconv2.hpp.txt](https://github.com/boostorg/charconv/files/15228527/charconv2.hpp.txt)  
>   
> Examples  
>   
> ```  
> namespace example  
> {  
> namespace C2 = boost::charconv2;  
> using array_type = std::array<char, 256>;  
>   
> template <typename Array, typename Result>  
> void do_println(const Array& arr, const Result& res)  
> {  
> 	if (res.ec == std::errc{})  
> 	{  
> 		std::cout << std::string_view{arr.data(), size_t(res.ptr-arr.data())};  
> 	}  
> 	else  
> 	{  
> 		std::cout << std::make_error_code(res.ec).message();  
> 	}  
> 	std::cout << std::endl;  
> }  
>   
> template <typename Type>  
> typename std::enable_if<boost::is_integer<Type>::value, void>::type	println(const Type value, const int base = 10)  
> {  
> 	array_type  
> 		arr{};  
> 	const C2::to_chars_result  
> 		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, base);  
> 	do_println(arr, res);  
> }  
>   
> template <typename Type>  
> typename std::enable_if<boost::is_floating_point<Type>::value, void>::type	println(const Type value, const C2::chars_format format, const int precision, const C2::nonfinite_style style = C2::nonfinite_style::standard)  
> {  
> 	array_type  
> 		arr{};  
> 	const C2::to_chars_result  
> 		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, format, precision, style);  
> 	do_println(arr, res);  
> }  
>   
> template <typename Type>  
> typename std::enable_if<boost::is_floating_point<Type>::value, void>::type	println(const Type value, const C2::chars_format format, const C2::nonfinite_style style = C2::nonfinite_style::standard)  
> {  
> 	array_type  
> 		arr{};  
> 	const C2::to_chars_result  
> 		res = C2::to_chars(arr.data(), arr.data()+arr.size(), value, format, style);  
> 	do_println(arr, res);  
> }  
>   
>   
> template <typename Type>  
> typename std::enable_if<boost::is_number<Type>::value, void>::type	println_binary(const Type value, const C2::binary_style style = C2::binary_style::standard)  
> {  
> 	array_type  
> 		arr{};  
> 	const C2::to_chars_result  
> 		res = C2::to_chars_binary(arr.data(), arr.data()+arr.size(), value, style);  
> 	do_println(arr, res);  
> }  
>   
> }	//	example  
>   
> int main()  
> {  
> 	namespace C2 = boost::charconv2;  
> 	using F = boost::float64_t;  
> 	using I = int;  
>   
> 	I i = 42;  
> 	example::println(i);  
> 	example::println_binary(i);  
> 	example::println_binary(i, C2::binary_style::showpoint);  
> 	std::cout << std::endl;  
>   
> 	F f = 42;  
> 	example::println(f, C2::chars_format::general);  
> 	example::println(f, C2::chars_format::general, C2::nonfinite_style::explicite_nan);  
> 	example::println_binary(f);  
> 	example::println_binary(f, C2::binary_style::showpoint);  
> 	std::cout << std::endl;  
>   
> 	f = std::numeric_limits<F>::signaling_NaN();  
> 	example::println(f, C2::chars_format::general);  
> 	example::println(f, C2::chars_format::general, C2::nonfinite_style::explicite_nan);  
> 	example::println_binary(f);  
> 	example::println_binary(f, C2::binary_style::showpoint);  
> 	std::cout << std::endl;  
>   
> 	return EXIT_SUCCESS;  
> }  
> ```  
>   
> Results  
>   
> ```  
> 42  
> 00000000000000000000000000101010  
> 00000000.00000000.00000000.00101010  
>   
> 42  
> 42  
> 0100000001000101000000000000000000000000000000000000000000000000  
> 0.10000000100.0101000000000000000000000000000000000000000000000000  
>   
> nan  
> snan  
> 0111111111110100000000000000000000000000000000000000000000000000  
> 0.11111111111.0100000000000000000000000000000000000000000000000000  
> ```  
>   
> What is your opinion on this? I would be delighted to receive your comments.  
>   
> regards Gero  
>   
> Edit I just realized that this will not work for you. This is because I have rewritten boost::math::cstdfloat -> into a separate library boost::cstdfloat. Why?  
>   
> * boost::math::cstdfloat contains many errors  
> * boost::math::cstdfloat is implemented in a very complicated and outdated style; nobody can understand it anymore -> unmaintainable  
>   
  
Are you intending to open a PR in math with the changes?   
  
> Here for example my types.hpp vs. boost::math::cstdfloat::cstdfloat_types.hpp [types.hpp.txt](https://github.com/boostorg/charconv/files/15229723/types.hpp.txt)

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-05-07 13:35:45 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2098427499  

> > Hello Matt, I would like to provide an extended library boost::charconv2. This implements:  
> >   
> > * FP types: an additional parameter nonfinite_style which means whether to distinguish between quiet_NaN and signaling_NaN. The default is no distinction - which ensures compatibility with std::from/to_chars. Unfortunately, your boost::charchonv does not behave according to the standard, which is why this measure is necessary.  
>   
> This is not the first time you have made this claim (see: [boostorg/math#1118](https://github.com/boostorg/math/issues/1118)) without any backing. I ask you please:  
>   
>     1. Quote a valid piece of the standard that says I am incorrect  
>   
>     2. Open a valid bug report  
>   
>     3. Stop making this claim  
>   
>   
> As far as I am concerned the standard says there is only implementation specific compatibility for roundtripping values, and I know that qNaNs and sNaNs are distinguished and roundtripped correctly here. Quote from section 22.13.2 Note 1 `[Note 1 : This guarantee applies only if to_chars and from_chars are executed on the same implementation. — end note ]`  
  
Hello Matt,  
I had already written a bug report and have updated it again:  
https://github.com/boostorg/charconv/issues/114  
  
thx  
Gero

---

## Comment 3

> Username: mborland  
> Created at: 2024-05-08 06:41:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2099851495  

> > > Hello Matt, I would like to provide an extended library boost::charconv2. This implements:  
> > >   
> > > * FP types: an additional parameter nonfinite_style which means whether to distinguish between quiet_NaN and signaling_NaN. The default is no distinction - which ensures compatibility with std::from/to_chars. Unfortunately, your boost::charchonv does not behave according to the standard, which is why this measure is necessary.  
> >   
> >   
> > This is not the first time you have made this claim (see: [boostorg/math#1118](https://github.com/boostorg/math/issues/1118)) without any backing. I ask you please:  
> > ```  
> > 1. Quote a valid piece of the standard that says I am incorrect  
> >   
> > 2. Open a valid bug report  
> >   
> > 3. Stop making this claim  
> > ```  
> >   
> >   
> >       
> >         
> >       
> >   
> >         
> >       
> >   
> >       
> >     
> > As far as I am concerned the standard says there is only implementation specific compatibility for roundtripping values, and I know that qNaNs and sNaNs are distinguished and roundtripped correctly here. Quote from section 22.13.2 Note 1 `[Note 1 : This guarantee applies only if to_chars and from_chars are executed on the same implementation. — end note ]`  
>   
> Hello Matt, I had already written a bug report and have updated it again: #114  
>   
> thx Gero  
  
That's not a bug that is a deliberate design choice that is legal under C99 definition of `printf`. See about halfway down on this page: https://learn.microsoft.com/en-us/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions?view=msvc-170. MSVC is not the only compiler that formats like that either: https://github.com/boostorg/charconv/blob/develop/test/to_chars_float_STL_comp.cpp#L132. Should you decide to roundtrip (which again has no guarantees per the standard) a sNaN from boost.charconv to GCC <charconv> it is processed without error, but will become a qNaN: https://godbolt.org/z/sxzrMsbE3.  
  
My rationale for following the MSVC and Clang handling of qNaN and sNaN is that they are entirely distinct values as defined in IEEE 754, so that should be reflected in the output.

---

## Comment 4

> Username: gpeterhoff  
> Created at: 2024-05-15 12:56:54 UTC  
> Updated at: 2024-05-15 20:19:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2112449031  

Hello Matt  
> That's not a bug that is a deliberate design choice that is legal under C99 definition of `printf`.   
  
This makes sense in principle; see below.  
  
> See about halfway down on this page: https://learn.microsoft.com/en-us/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions?view=msvc-170. MSVC is not the only compiler that formats like that either: https://github.com/boostorg/charconv/blob/develop/test/to_chars_float_STL_comp.cpp#L132  
  
This statement is probably not correct. Only MSVC seems to support the string values "nan(ind)", "nan(snan)" and "-nan(snan)". In any case, gcc, clang and intel do not: https://godbolt.org/z/j7Y3nhEYd  
That is the problem.  
  
Intermediate question about MSVC (I don't have it)  
Which string values deliver:  
- std::cout << numeric_limits::±quiet_NaN()/±signaling_NaN() ?  
- std::to_string(numeric_limits::±quiet_NaN()/±signaling_NaN()) ?  
  
As I said, it is a problem that MSVC (once again) deviates. But how should this be dealt with? There are various possibilities:  
- The corresponding string values for ±qnan/±snan could be provided compiler/OS-specifically.  
- The corresponding string values for ±qnan/±snan could be provided uniformly for all compilers/OS.  
- However, you have opted exclusively for the MSVC behavior. However, this is incompatible with all other compilers/OS.  
  
This also applies to parsing (from_chars)  
- Should only compilers/OS specifically match string values ?  
- Should all legal string values (i.e. also those that are not actually supported by the compiler/OS) be matched ?  
- Should a distinction be made between qnan/snan ? If yes: which string values should be supported ?  
  
This is clearly regulated with my charconv2. The additional parameter specifies whether a distinction should be made between qnan/snan at all:  
- nonfinite_style::standard -> No distinction is made between qnan/snan.  
- nonfinite_style::explicite_nan -> A distinction is made between qnan/snan; the corresponding string values are 1., i.e. uniform for all compilers/OS + "-nan"/"nan".  
  
It might also be possible to provide differently named functions (such as boost::charconv::from_chars_erange), which provide this functionality first. But then boost::charconv::from/to_chars would have to be 100% compatible (with all compilers on all OS) to std::from/to_chars.  
  
Hints  
- My string values 1. are of course arbitrarily chosen, but make sense.  
- You urgently need to revise your functions in which non-finite values are output or parsed (independent of my suggestions). You already include <boost/core/detail/string_view.hpp>, which is why the string constants should also be provided as (static constexpr) boost::core::detail::string_view. This saves you various memcpy orgies (manual specification of the string length), which is extremely dangerous. After all, we are talking about C++ and not C. It is also flexible if the string constants have to be adapted.  
  
1. "-qnan"/"qnan" and "-snan"/"snan"  
  
regards  
Gero

---

## Comment 5

> Username: gpeterhoff  
> Created at: 2024-05-15 13:59:43 UTC  
> Updated at: 2024-05-15 14:00:23 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2112628687  

Hello Matt  
> > * from/to_chars_binary  
>   
> For floating point types? This can also be trivially implemented on the user end by `std::memcpy`/`std::bit_cast` into an integer and then using base = 2.  
  
No, the behavior is different. The values must be fully qualified, i.e. contain at least as many digits as the corresponding type contains relevant bits. All subsequent digits are ignored.  
- binary_style::standard  
 No points are output or parsed.  
- binary_style::showpoint  
 a) INT: '.' is output or parsed every 8 digits/bits.  
 b) FP: '.' is output or parsed after the sign bit and the exponent.  
  
Why do I provide this functionality?  
If you are dealing with INT/FP at bit level, you want to see whether a function is working correctly. from/to_chars_binary are bit dumps, enriched with separators at the relevant positions if required.  
  
regards  
Gero

---

## Comment 6

> Username: mborland  
> Created at: 2024-05-17 06:48:02 UTC  
> Url: https://github.com/boostorg/charconv/issues/195#issuecomment-2116868929  

> Hello Matt  
>   
> > > * from/to_chars_binary  
> >   
> >   
> > For floating point types? This can also be trivially implemented on the user end by `std::memcpy`/`std::bit_cast` into an integer and then using base = 2.  
>   
> No, the behavior is different. The values must be fully qualified, i.e. contain at least as many digits as the corresponding type contains relevant bits. All subsequent digits are ignored.  
>   
> * binary_style::standard  
>   No points are output or parsed.  
> * binary_style::showpoint  
>   a) INT: '.' is output or parsed every 8 digits/bits.  
>   b) FP: '.' is output or parsed after the sign bit and the exponent.  
>   
> Why do I provide this functionality? If you are dealing with INT/FP at bit level, you want to see whether a function is working correctly. from/to_chars_binary are bit dumps, enriched with separators at the relevant positions if required.  
>   
> regards Gero  
  
If you open a PR with just this feature, and the associated testing I'll take a look because I can see the utility in it. The rest I can't say I want to pursue.
