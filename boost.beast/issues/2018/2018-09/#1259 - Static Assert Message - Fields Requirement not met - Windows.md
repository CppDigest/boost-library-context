# #1259 - Static Assert Message - Fields Requirement not met - Windows [Closed]

> Username: simmse  
> Created at: 2018-09-27 21:20:33 UTC  
> Updated at: 2018-11-28 02:09:57 UTC  
> Closed at: 2018-11-28 02:09:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1259  

Hello Everyone,  
  
The latest Intel 19.0 C++ Windows compiler continues to struggle with Beast and/or ASIO.  Note that the  Intel C++ Windows compiler uses the language header files supplied by Microsoft.  The highest language standard possible is C++14.  The attached zipped solution has the standard set to C++14.  
[BeastTestZip.zip](https://github.com/boostorg/beast/files/2425945/BeastTestZip.zip)  
  
### Version of Beast = 181  
  
### Steps necessary to reproduce the problem  
Compile the attached Visual Studio 2017, with Intel 19.0 solution.  The code is almost identical to the sample Beast SSL HTTP Example.  It is assumed that the Boost path is setup appropriately.  Inside the project is an environment variable to reach the include directory called BOOST_INC.  
  
### All relevant compiler information  
Intel 19.0 C++ Windows compiler, integrated with Visual Studio Enterprise 15.8.4  
  
The complete compiler messages are below.  The main issue is that a static_assert message occurs.  The message reads:  
  
error : static assertion failed with "Fields requirements not met"  
  
The last one, immediately above the Visual Studio compilation summary output, indicates line 98 of the BeastTest.cpp file.  
  
1>------ Rebuild All started: Project: BeastTest, Configuration: Release x64 ------  
1>pch.cpp  
1>BeastTest.cpp  
1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
1>- add -D_WIN32_WINNT=0x0501 to the compiler command line; or  
1>- add _WIN32_WINNT=0x0501 to your project's Preprocessor Definitions.  
1>Assuming _WIN32_WINNT=0x0501 (i.e. Windows XP target).  
1>C:\Boost\include\boost-1_68\boost/beast/core/impl/file_stdio.ipp(108): warning #1786: function "fopen" (declared at line 208 of "C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\ucrt\stdio.h") was declared deprecated ("This function or variable may be unsafe. Consider using fopen_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.")  
1>      f_ = std::fopen(path, s);  
1>                ^  
1>  
1>C:\Boost\include\boost-1_68\boost/date_time/c_time.hpp(101): warning #1786: function "localtime" (declared at line 506 of "C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\ucrt\time.h") was declared deprecated ("This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.")  
1>          result = std::localtime(t);  
1>                        ^  
1>  
1>C:\Boost\include\boost-1_68\boost/date_time/c_time.hpp(110): warning #1786: function "gmtime" (declared at line 496 of "C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\ucrt\time.h") was declared deprecated ("This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.")  
1>          result = std::gmtime(t);  
1>                        ^  
1>  
1>C:\Boost\include\boost-1_68\boost/beast/http/message.hpp(60): error : static assertion failed with "Fields requirements not met"  
1>      static_assert(is_fields<Fields>::value,  
1>      ^  
1>          detected during:  
1>            instantiation of class "boost::beast::http::header<1, Fields> [with Fields=boost::beast::http::fields]" at line 491  
1>            instantiation of class "boost::beast::http::message<isRequest, Body, Fields> [with isRequest=1, Body=boost::beast::http::string_body, Fields=boost::beast::http::fields]" at line 87 of "BeastTest.cpp"  
1>  
1>C:\Boost\include\boost-1_68\boost/beast/http/message.hpp(60): error : static assertion failed with "Fields requirements not met"  
1>      static_assert(is_fields<Fields>::value,  
1>      ^  
1>          detected during:  
1>            instantiation of class "boost::beast::http::header<1, Fields> [with Fields=boost::beast::http::detail::fields_model]" at line 831 of "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.15.26726\include\type_traits"  
1>            instantiation of class "std::is_constructible<_Ty, _Args...> [with _Ty=boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char>>::writer, _Args=<boost::beast::http::header<1, boost::beast::http::detail::fields_model> &, std::basic_string<char, std::char_traits<char>, std::allocator<char>> &>]" at line 126 of "C:\Boost\include\boost-1_68\boost/beast/http/type_traits.hpp"  
1>            instantiation of class "boost::beast::http::is_mutable_body_writer<T, boost::beast::detail::void_t<T::writer, T::writer::const_buffers_type, decltype((<expression>))>> [with T=boost::beast::http::string_body]" at line 92 of "BeastTest.cpp"  
1>  
1>C:\Boost\include\boost-1_68\boost/beast/http/message.hpp(263): error : static assertion failed with "Fields requirements not met"  
1>      static_assert(is_fields<Fields>::value,  
1>      ^  
1>          detected during:  
1>            instantiation of class "boost::beast::http::header<0, Fields> [with Fields=boost::beast::http::detail::fields_model]" at line 831 of "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.15.26726\include\type_traits"  
1>            instantiation of class "std::is_constructible<_Ty, _Args...> [with _Ty=boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char>>::writer, _Args=<boost::beast::http::header<0, boost::beast::http::detail::fields_model> &, std::basic_string<char, std::char_traits<char>, std::allocator<char>> &>]" at line 129 of "C:\Boost\include\boost-1_68\boost/beast/http/type_traits.hpp"  
1>            instantiation of class "boost::beast::http::is_mutable_body_writer<T, boost::beast::detail::void_t<T::writer, T::writer::const_buffers_type, decltype((<expression>))>> [with T=boost::beast::http::string_body]" at line 92 of "BeastTest.cpp"  
1>  
1>C:\Boost\include\boost-1_68\boost/beast/http/message.hpp(263): error : static assertion failed with "Fields requirements not met"  
1>      static_assert(is_fields<Fields>::value,  
1>      ^  
1>          detected during:  
1>            instantiation of class "boost::beast::http::header<0, Fields> [with Fields=boost::beast::http::fields]" at line 491  
1>            instantiation of class "boost::beast::http::message<isRequest, Body, Fields> [with isRequest=0, Body=boost::beast::http::dynamic_body, Fields=boost::beast::http::fields]" at line 98 of "BeastTest.cpp"  
1>  
1>compilation aborted for BeastTest.cpp (code 2)  
1>Done building project "BeastTest.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
  
Sincerely,  
  
simmse

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-29 13:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1259#issuecomment-425645365  

Well, you're going to have to figure this one out and it will take a bit of work. I will show you the technique. Here is the metafunction which is failing for you:  
https://github.com/boostorg/beast/blob/65022367bb53ab1e77d2aca7fbb264b3547be331/include/boost/beast/http/type_traits.hpp#L220  
The actual implementation, `detail::is_fields_helper`, is here:  
https://github.com/boostorg/beast/blob/65022367bb53ab1e77d2aca7fbb264b3547be331/include/boost/beast/http/detail/type_traits.hpp#L100  
  
Notice how the metafunction has a series of type aliases `t1`, `t2`, ... `t12`. Each of these is an alias for either `std::true_type` or `std::false_type` depending on the SFINAE-based checks in the statements which come immediately before. Here's an example:  
```  
    template<class U = is_fields_helper>  
    static auto f1(int) -> decltype(  
        std::declval<string_view&>() = std::declval<U const&>().get_method_impl(),  
        std::true_type());  
    static auto f1(...) -> std::false_type;  
    using t1 = decltype(f1(0));  
```  
  
Then at the very end we combine them all together:  
```  
    using type = std::integral_constant<bool,  
         t1::value &&  t2::value && t3::value &&  
         t4::value &&  t5::value && t6::value &&  
         t7::value &&  t8::value && t9::value &&  
        t10::value && t11::value && t12::value>;  
```  
  
What you need to do is start commenting out parts of the right side of the assignment to `type` above. You can use a binary-search technique. For example, start by commenting out the bottom half:  
  
```  
    using type = std::integral_constant<bool,  
         t1::value &&  t2::value && t3::value &&  
         t4::value &&  t5::value && t6::value /* &&  
         t7::value &&  t8::value && t9::value &&  
        t10::value && t11::value && t12::value */>;  
```  
  
Keep adjusting the commented out parts until you figure out exactly which of the values are responsible for the failure. Then report your results in this issue and we can identify the declaration that the compiler is having trouble with.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1259#issuecomment-441859462  

This issue has been open for a while with no activity, has it been resolved?
