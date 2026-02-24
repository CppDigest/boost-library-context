# #62 - Questions and Comments [Closed]

> Username: gpeterhoff  
> Created at: 2023-07-08 17:49:12 UTC  
> Updated at: 2023-10-16 09:20:59 UTC  
> Closed at: 2023-10-16 09:20:59 UTC  
> Url: https://github.com/boostorg/charconv/issues/62  

Hi Matt,  
Questions  
1) supported character types  
I see that you are implementing from_chars_result_t as a template.  
Do you plan to provide from_chars/to_chars for all character types (char32/16/8_t, wchar_t, char)?  
  
2) multiprecision  
Are from_chars/to_chars also provided for boost::multiprecision types?  
If not, are from_chars/to_chars implemented in boost::multiprecision itself?  
  
Comments  
1) Conversion  
If std::charconv is available I can well imagine that between the types chars_format/from_chars_result/to_chars_result of std::charconv and boost::charconv also times must be converted. You could write functions for this. But I think it makes more sense to provide the boost types with conversions (ctor, cast-operator):  
from_chars_result: [from_chars_result.hpp.txt](https://github.com/cppalliance/charconv/files/11991391/from_chars_result.hpp.txt) in the template specialization  
to_chars_result: [to_chars_result.hpp.txt](https://github.com/cppalliance/charconv/files/11991388/to_chars_result.hpp.txt)  
chars_format: [chars_format.hpp.txt](https://github.com/cppalliance/charconv/files/11991385/chars_format.hpp.txt) Unfortunately it is not possible to implement cast operators for enum_a -> enum_b too directly. Therefore I go the way over a struct.  
  
2) Usage  
If std::charconv is available you can use this.  
  
3) Comparison operators  
In case you wonder why I adapted the comparison operators; this has the following background:  
gcc very often generates conditional gaps when evaluating the arguments of logical links (&& ||). The result is that the generated code is full of conditional jumps and it increases the code size significantly. This is not cache friendly. In addition, possible cmov's are overlooked/not generated.  
With the "int-trick", the binary linking of the arguments, this does not happen. Of course *all* arguments are evaluated. If necessary, only *exactly one* conditional jump is generated.  
This strategy - to generate branchfree code if possible - I find better.  
  
3) Warnings  
At the moment there are still a lot of warnings (>350) generated concerning conversions Unsigned <-> Signed.  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-08-14 15:46:18 UTC  
> Url: https://github.com/boostorg/charconv/issues/62#issuecomment-1677587624  

> 1. supported character types  
>    I see that you are implementing from_chars_result_t as a template.  
>    Do you plan to provide from_chars/to_chars for all character types (char32/16/8_t, wchar_t, char)?  
  
I do not. It's written as a template to make it compatible with `fast_float` since he templates the character type. The C++ standard only specifies `char` support.  
  
> 2. multiprecision  
>    Are from_chars/to_chars also provided for boost::multiprecision types?  
>    If not, are from_chars/to_chars implemented in boost::multiprecision itself?  
>   
  
There is a rough equivalency already in multiprecision since they allow you to dump the representation to string, and have a string constructor. Integer types could theoretically be supported with the algorithms in here, but without much benefit.  
  
> Comments  
>   
> 1. Conversion  
>    If std::charconv is available I can well imagine that between the types chars_format/from_chars_result/to_chars_result of std::charconv and boost::charconv also times must be converted. You could write functions for this. But I think it makes more sense to provide the boost types with conversions (ctor, cast-operator):  
>    from_chars_result: [from_chars_result.hpp.txt](https://github.com/cppalliance/charconv/files/11991391/from_chars_result.hpp.txt) in the template specialization  
>    to_chars_result: [to_chars_result.hpp.txt](https://github.com/cppalliance/charconv/files/11991388/to_chars_result.hpp.txt)  
>    chars_format: [chars_format.hpp.txt](https://github.com/cppalliance/charconv/files/11991385/chars_format.hpp.txt) Unfortunately it is not possible to implement cast operators for enum_a -> enum_b too directly. Therefore I go the way over a struct.  
  
This could be useful. I have conversion functions in the test suite to compare the results already.  
  
> 2. Usage  
>    If std::charconv is available you can use this.  
  
For all types this implementation meets, or exceeds the performance of existing charconv implementations so I don't think we need to fall back to the STL versions.  
  
> 3. Comparison operators  
>    In case you wonder why I adapted the comparison operators; this has the following background:  
>    gcc very often generates conditional gaps when evaluating the arguments of logical links (&& ||). The result is that the generated code is full of conditional jumps and it increases the code size significantly. This is not cache friendly. In addition, possible cmov's are overlooked/not generated.  
>    With the "int-trick", the binary linking of the arguments, this does not happen. Of course _all_ arguments are evaluated. If necessary, only _exactly one_ conditional jump is generated.  
>    This strategy - to generate branchfree code if possible - I find better.  
  
I'll take a look and see what can be adapted; some of the comparisons are designed to be short-circuited since I am missing C++17s `if constexpr`.  
  
> 4. Warnings  
>    At the moment there are still a lot of warnings (>350) generated concerning conversions Unsigned <-> Signed.  
  
I will take a look at this as well. The build and tests are clean when built with `-Wextra -Werror`, but I believe that `-Wsign-compare` doesn't show up until `-Wpedantic`.
