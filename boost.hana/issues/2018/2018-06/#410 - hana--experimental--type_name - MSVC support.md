# #410 - hana::experimental::type_name - MSVC support? [Open]

> Username: danielchen0  
> Created at: 2018-06-08 20:53:21 UTC  
> Updated at: 2018-06-11 18:32:51 UTC  
> Url: https://github.com/boostorg/hana/issues/410  

With the recent [fork provided by Microsoft](https://blogs.msdn.microsoft.com/vcblog/2018/06/04/use-boost-hana-via-vcpkg-with-the-latest-msvc-compiler/)  for Boost Hana / MSVC compiler compatibility, Hana now builds with the MSVC compiler. However, the experimental method hana::experimental::type_name has a hardcoded switch in its helper "type_name_impl2()" that only supports Clang:  
  
	// Note: We substract the null terminator from the string sizes below.  
	template <typename T>  
	constexpr cstring type_name_impl2() {  
  
	#if defined(__clang__)  
		constexpr char const* pretty_function = __PRETTY_FUNCTION__;  
		constexpr std::size_t total_size = sizeof(__PRETTY_FUNCTION__) - 1;  
		constexpr std::size_t prefix_size = sizeof("boost::hana::experimental::detail::cstring boost::hana::experimental::detail::type_name_impl2() [T = ") - 1;  
		constexpr std::size_t suffix_size = sizeof("]") - 1;  
	#else  
            #error "No support for this compiler."  
	#endif  
  
There exist pseudo-equivalent MSVC predefined-macros ([see this doc](https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros)) to `__PRETTY_FUNCTION__` : `__FUNCSIG__, __FUNCDNAME__, __func__, ` and `__FUNCTION__`. See [this](https://stackoverflow.com/questions/4434282/visual-c-equivalent-of-file-line-and-pretty-function/4434362#comment88540256_4434362) and [this](https://stackoverflow.com/questions/4384765/whats-the-difference-between-pretty-function-function-func) for reference. It is also worth looking at [BOOST_CURRENT_FUNCTION](https://www.boost.org/doc/libs/1_42_0/libs/utility/current_function.html). These could possibly make type_name compatible with MSVC by replacing the `__PRETTY_FUNCTION__` macro.  
  
I tried adding this switch in the type_name.hpp header file, which allows MSVC to hit the switch and use type_name:  
  
		#if defined (__clang__)  
		...  
		#elif defined(_MSC_VER)  
			constexpr char const* pretty_function = __FUNCSIG__;  
			constexpr std::size_t total_size = sizeof(__FUNCSIG__) - 1;  
			constexpr std::size_t prefix_size = sizeof("boost::hana::experimental::detail::cstring boost::hana::experimental::detail::type_name_impl2() [T = ") - 1;  
			constexpr std::size_t suffix_size = sizeof("]") - 1;  
		#else  
	            #error "No support for this compiler."  
		#endif  
  
type_name then yields typename strings like this:  
  
            int -> "e_impl2<int>(void"  
            "myStruct" user-defined struct -> "e_impl2<struct myStruct>(void"  
            std::string -> "e_impl2<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >>(void"  
  
Using `BOOST_CURRENT_FUNCTION` gives the same result.  
This sort of output is messy, but can be interpreted and processed to give a better result.  
  
`__FUNCDNAME__, __func__,` and `__FUNCTION__` all cause compilation errors when used like above, though I suspect they would provide better output "out-of-the-box" if they can be worked out.  
   
Would you consider adding something like the above so that MSVC can have type_name support? I may be interested in writing some code myself for solving the problem, though I think the fix is not complicated.
