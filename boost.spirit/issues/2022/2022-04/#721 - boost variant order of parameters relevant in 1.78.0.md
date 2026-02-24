# #721 - boost variant order of parameters relevant in 1.78.0 ? [Open]

> Username: allopislozano  
> Created at: 2022-04-05 12:35:18 UTC  
> Updated at: 2025-05-09 23:36:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/721  

Some of our code broke after the 1.78.0 update.  I finally managed to pinpoint the issue. It looks like for some reason a grammar that is a disjunction needs the same order of parameters in the variant to be synthesized. Was this something that should not have compiled in 1.77 or is it something that broke in 78?   
  
The following code works in 77 but not in 78:  
https://godbolt.org/z/MKMezMTqq  
  
After switching the order of the parameters in the X variant   from B,A to A,B  it works in both versions:  
https://godbolt.org/z/6e6sMKTYW

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-12 19:38:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/721#issuecomment-1244257033  

Pulling the repro from #722 here:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/variant/variant.hpp>  
#include <boost/fusion/include/std_pair.hpp>  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
    using namespace x3;  
  
    char const* s = "";  
  
    struct A {};  
    struct B {};  
    struct C {};  
    boost::variant<  
        std::pair<int, boost::variant<B, A>>,  
        C  
    > target;  
  
    x3::parse(s, s,  
  
        attr(int{}) >> (attr(A{}) | attr(B{}))  
      | attr(C{})  
  
      , target);  
}  
```

---

## Comment 2

> Username: cppljevans  
> Created at: 2022-11-03 23:36:55 UTC  
> Updated at: 2022-11-03 23:56:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/721#issuecomment-1302792460  

Does this also solve https://github.com/boostorg/spirit/issues/707 ?  
Test case for 707 is located here:  
  https://github.com/cppljevans/spirit-experiments/blob/develop/test/x3/issue707.cpp

---

## Comment 3

> Username: cppljevans  
> Created at: 2022-11-04 08:20:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/721#issuecomment-1303105896  

> Does this also solve #707 ? Test case for 707 is located here: https://github.com/cppljevans/spirit-experiments/blob/develop/test/x3/issue707.cpp  
  
Apparently it doesn't solve https://github.com/boostorg/spirit/issues/707 because when I tried compiling the issue707.cpp test case, I got errors.  The start of the error messages were:  
```  
clang++ -c -O0 -gdwarf-2 -g3 -ftemplate-backtrace-limit=0  -std=c++2b -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -DBOOST_NO_CXX98_FUNCTION_BASE  -I../../include -I/home/evansl/prog_dev/boost.org/1_80_0download/download    -ftemplate-depth=200  issue707.cpp -MMD -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/1_80_0download/download/sandbox/eido79/variant_substitute/test/x3-80/issue707.o   
In file included from issue707.cpp:8:  
In file included from ../../include/boost/spirit/home/x3.hpp:62:  
In file included from ../../include/boost/spirit/home/x3/auxiliary.hpp:11:  
In file included from ../../include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17:  
../../include/boost/spirit/home/x3/support/traits/move_to.hpp:196:9: error: no matching function for call to 'move_to'  
        detail::move_to(src, dest, typename attribute_category<Dest>::type());  
        ^~~~~~~~~~~~~~~  
../../include/boost/spirit/home/x3/auxiliary/attr.hpp:66:21: note: in instantiation of function template specialization 'boost::spirit::x3::traits::move_to<const issue707::D &, issue707::uda_data>' requested here  
            traits::move_to(value_, attr_);  
                    ^  
../../include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:97:25: note: in instantiation of function template specialization 'boost::spirit::x3::attr_parser<issue707::D>::parse<const char *, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type, issue707::uda_data>' requested here  
            if (!parser.parse(first, last, context, rcontext, val))  
                        ^  
../../include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:128:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_into_container_base_impl<boost::spirit::x3::attr_parser<issue707::D>>::call_synthesize_x<const char *, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type, std::vector<issue707::uda_data>>' requested here  
            return call_synthesize_x(parser, first, last, context, rcontext, attr  
                   ^  
  
```
