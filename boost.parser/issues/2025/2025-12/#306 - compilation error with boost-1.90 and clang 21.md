# #306 - compilation error with boost-1.90 and clang 21 [Closed]

> Username: vmauery  
> Created at: 2025-12-19 22:14:37 UTC  
> Updated at: 2026-01-25 23:27:35 UTC  
> Closed at: 2026-01-25 23:27:35 UTC  
> Url: https://github.com/boostorg/parser/issues/306  

I have a project that was working fine with boost-1.87. I updated my system and got boost-1.90. Now it is failing to compile. I did a git bisect and found that the change that broke things is  
```  
commit 821d1d4c084ae9d6ab9a1d83a5113701c934b22c  
Author: Zach Laine <whatwasthataddress@gmail.com>  
Date:   Thu Feb 20 23:15:30 2025 -0600  
  
    Fix longstanding mysterious ill-formedness in  
    detail::static_assert_merge_attributes.  It was down to unavailabilty of a  
    default ctor for certain parser types.  
```  
When I revert this change on top of boost-1.90, the code compiles again.  
I don't know how much difference this makes, but it is a system running fedora 43 with clang++ 21.1.7  
  
The error messages I see are like this:   
```  
clang++ -Isrc/libcommon.a.p -Isrc/ -I../src -I. -I.. -Isrc -I/usr/local/boost -flto -fdiagnostics-color=always -D_GLIBCXX_ASSERTIONS=1 -D_FILE_OFFSET_BITS=64 -Wall -Winvalid-pch -Wextra -Wpedantic -Werror -std=c++23 -O0 -g -DUSE_MPFR_BACKEND -Wcast-align -Wdouble-promotion -Wformat=2 -Wmisleading-indentation -Wno-reorder -Wnon-virtual-dtor -Woverloaded-virtual -Wno-deprecated-literal-operator -Wunused-but-set-parameter -Wunused-but-set-variable -Wunused-function -Wunused-label -Wunused-local-typedefs -Wunused-macros -Wno-unused-result -Wunused-value -Wno-unused-variable -Wno-unused-parameter -Wno-double-promotion -DHAVE_READLINE -DBOOST_ASIO_DISABLE_THREADS -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_NO_RTTI -DBOOST_NO_TYPEID -DBOOST_SYSTEM_NO_DEPRECATED -fPIC -D_DEFAULT_SOURCE -D_XOPEN_SOURCE=600 -DBOOST_ALL_NO_LIB -MD -MQ src/libcommon.a.p/parser.cpp.o -MF src/libcommon.a.p/parser.cpp.o.d -o src/libcommon.a.p/parser.cpp.o -c ../src/parser.cpp  
../src/parser.cpp:635:11: error: non-const lvalue reference to type 'conditional_t<...>' cannot bind to a temporary of type 'conditional_t<...>'  
  635 |     auto& val = _val(ctx);  
      |           ^     ~~~~~~~~~  
/usr/local/boost/boost/parser/parser.hpp:4732:51: note: in instantiation of function template specialization 'smrty::parser::(anonymous namespace)::(anonymous class)::operator()<const boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler, const boost::parser::detail::nope, boost::parser::detail::nope, std::basic_string<char>, boost::parser::detail::nope, void, boost::parser::detail::nope, boost::parser::detail::nope, std::ranges::subrange<const char *>>>' requested here  
 4732 |                                          decltype(action_(action_context)),  
      |                                                   ^  
/usr/local/boost/boost/parser/parser.hpp:4683:13: note: in instantiation of function template specialization 'boost::parser::action_parser<boost::parser::seq_parser<std::tuple<boost::parser::digit_parser, boost::parser::digit_parser, boost::parser::digit_parser, boost::parser::digit_parser>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, smrty::parser::(anonymous namespace)::(lambda at ../src/parser.cpp:633:24)>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler>, boost::parser::parser_interface<boost::parser::ws_parser<false, false>>, boost::parser::detail::nope>' requested here  
 4683 |             call(first, last, context, skip, flags, success, retval);  
      |             ^  
/usr/local/boost/boost/parser/parser.hpp:3873:31: note: in instantiation of function template specialization 'boost::parser::action_parser<boost::parser::seq_parser<std::tuple<boost::parser::digit_parser, boost::parser::digit_parser, boost::parser::digit_parser, boost::parser::digit_parser>, std::tuple<std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>, std::integral_constant<bool, true>>, std::tuple<std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>, std::integral_constant<long long, 0>>>, smrty::parser::(anonymous namespace)::(lambda at ../src/parser.cpp:633:24)>::call<const char *, const char *, boost::parser::detail::parse_context<false, false, const char *, const char *, boost::parser::default_error_handler>, boost::parser::parser_interface<boost::parser::ws_parser<false, false>>>' requested here  
 3873 |                 return parser.call(  
      |                               ^  
```  
  
The code in question:  
```  
struct time_parts  
{  
    time_parts() :  
        absolute(false), year(), month(), day(), h(), m(), s(), sub(), tz(),  
        duration(), suffix(), full()  
    {  
    }  
    // amended ISO8601 format: // yyyy-mm-dd[Thh:mm:ss[.sub]][Z|([+-]hh:mm)]  
    bool absolute;  
    std::string year;  
    std::string month;  
    std::string day;  
    std::string h;  
    std::string m;  
    std::string s;  
    std::string sub;  
    std::string tz;  
    single_number_parts duration;  
    std::string suffix;  
    std::string full;  
};  
bp::rule<class time, time_parts> const time = "time";  
  
auto const save_year = [](auto& ctx) {  
    const auto& attr = _attr(ctx);  
    auto& val = _val(ctx);  
    val.year = attr;  
    val.absolute = true;  
};  
  
// iso 8601 date format  
auto const time_def = bp::lexeme  
    [bp::merge[(bp::digit >> bp::digit >> bp::digit >> bp::digit)[save_year] >>  
               bp::string("-") >> (bp::digit >> bp::digit)[save_month] >>  
               bp::string("-") >> (bp::digit >> bp::digit)[save_day] >>  
               bp::omit[-(  
                   bp::string("T") >> (bp::digit >> bp::digit)[save_hour] >>  
                   bp::string(":") >> (bp::digit >> bp::digit)[save_minute] >>  
                   bp::string(":") >> (bp::digit >> bp::digit)[save_second] >>  
                   -(bp::char_(".") >> +bp::digit)[save_sub_second] >>  
                   -(bp::char_("+-") >>  
                     (bp::digit >> bp::digit >> bp::char_(":") >> bp::digit >>  
                      bp::digit))[save_timezone])]][save_full_time]];  
```  
  
  
Any help here would be great. I am not sure if I am doing something wrong here or if the change I reverted really is the problem.

---

## Comment 1

> Username: tzlaine  
> Created at: 2026-01-25 23:27:31 UTC  
> Url: https://github.com/boostorg/parser/issues/306#issuecomment-3797396839  

It looks like the problem here is the use of `merge[]`.  Internally, merge tags each sub-parser as getting merged into the merged attribute.  `bp::merge[a >> b >> c]` makes one attribute, not a tuple of three, as you would expect from the name.  However, the attribute of `a` inside the `bp::merge` is `bp::none`, because it doesn't produce an attribute, it just contributes to the merged attribute.  
  
So, for you case, I think the parser you intended would do the following:  
  
1) save the iterator to the start of the `time_parts` parse;  
2) do all the parsing you wrote above, without the `bp::merge`;  
3) take the iterator to the end of the `time_parts` parse, and use it along with the saved begin-iterator to fill in `time_parts::full`.  
  
You can use a `bp::eps` at either end of the parse, and add a local to your rule in which to stuff the begin-iterator.  For example:  
  
```c++  
struct time_locals  
{  
    iterator_type first = {}; // whatever your iterator type is...  
};  
  
bp::rule<class time, time_parts, time_locals> const time = "time";  
  
auto const save_first = [](auto & ctx) {  
    _locals(ctx).first = _where(ctx).begin();  
};  
  
auto const save_first = [](auto & ctx) {  
    _val(ctx).full = std::string(_locals(ctx).first, _where(ctx).begin());  
};  
  
auto const time_p_def = bp::lexeme[  
    bp::eps[save_first] >> /* the rest of your parser, minus the merge */  
] >> bp::eps[save_full_time];  
```
