# #639 - [Classic] Build fails with C++20 standard and head of GCC11 [Closed]

> Username: stac47  
> Created at: 2021-01-21 15:19:47 UTC  
> Updated at: 2021-01-22 06:38:20 UTC  
> Closed at: 2021-01-22 06:38:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/639  

Hello,  
  
Building boost 1.75.0 fails with this freshly compiled `gcc (GCC) 11.0.0 20210119 (experimental)` kind of issues:  
```  
In file included from ./boost/spirit/include/classic_multi_pass.hpp:11,  
                 from ./boost/graph/graphviz.hpp:31,  
                 from libs/graph/src/read_graphviz_new.cpp:50:  
./boost/spirit/home/classic/iterator/multi_pass.hpp: In instantiation of 'void boost::spirit::classic::impl::mp_swap(T&, T&) [with T = long unsigned int*]':  
./boost/spirit/home/classic/iterator/multi_pass.hpp:78:41:   required from here  
./boost/spirit/home/classic/iterator/multi_pass.hpp:1289:40: error: 'void boost::spirit::classic::swap(boost::spirit::classic::multi_pass<InputT, InputPolicy, OwnershipPolicy, CheckingPolicy, StoragePolicy>&, boost::spirit::classic::multi_pass<InputT, InputPolicy, Owners  
hipPolicy, CheckingPolicy, StoragePolicy>&)' conflicts with a previous declaration  
 1289 |         using BOOST_SPIRIT_CLASSIC_NS::swap;  
      |                                        ^~~~  
In file included from ./boost/lexical_cast/detail/converter_lexical_streams.hpp:58,  
                 from ./boost/lexical_cast/detail/converter_lexical.hpp:54,  
                 from ./boost/lexical_cast/try_lexical_convert.hpp:44,  
                 from ./boost/lexical_cast.hpp:32,  
                 from ./boost/property_map/dynamic_property_map.hpp:23,  
                 from libs/graph/src/read_graphviz_new.cpp:32:  
/opt/1A/toolchain/x86_64-v21.0.15/include/c++/11.0.0/sstream:1206:5: note: previous declaration 'namespace std::__cxx11 { }::swap'  
 1206 |     swap(basic_stringstream<_CharT, _Traits, _Allocator>& __x,  
      |     ^~~~  
```  
This seems to come from a change in C++20 that is now in gcc development branch (it was not the case less than one month ago). The change in the ADL for C++20 is described here: https://en.cppreference.com/w/cpp/language/adl  
  
Removing all the:  
```  
using using BOOST_SPIRIT_CLASSIC_NS::swap;  
using boost::swap;  
```  
should fix the issue.  
Regards,  
  
PS: congratulation for this excellent library!

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-01-21 17:34:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/639#issuecomment-764815889  

Could your please provide a reproducer? I had no luck in triggering the error with https://godbolt.org/z/fb1rYe (`g++ (Compiler-Explorer-Build) 11.0.0 20210120 (experimental)`)

---

## Comment 2

> Username: stac47  
> Created at: 2021-01-21 18:41:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/639#issuecomment-764855822  

Doing a reproducer, I realized it is not C++20 specific. So it is a bug in gcc.  
Though my assertion remains valid: ADL will work with unqualified lookup of the template function.  
I let you decide if this is worth merging it (which I doubt): don't hesitate to close this PR.  
A reproducer:  
```  
#include <boost/spirit/include/classic_common.hpp>  
#include <boost/spirit/include/classic_core.hpp>  
#include <boost/spirit/include/classic_parse_tree.hpp>  
#include <boost/spirit/include/classic_utility.hpp>  
  
#include <iostream>  
#include <string>  
  
struct MappingValue{};  
  
typedef char const* iterator_t;  
typedef boost::spirit::classic::node_val_data_factory<MappingValue> factory_t;  
typedef boost::spirit::classic::tree_match<iterator_t, factory_t>  
    parse_tree_match_t;  
typedef parse_tree_match_t::const_tree_iterator iter_t;  
typedef parse_tree_match_t::node_t mapping_node_t;  
  
struct Mapping {  
    Mapping() {}  
  
    void operator()(mapping_node_t&, char const* str,  
                    char const* end) const {}  
  
    MappingValue _value;  
};  
  
struct my_grammar : public boost::spirit::classic::grammar<my_grammar> {  
    virtual ~my_grammar(){};  
    template <typename ScannerT>  
    struct definition {  
        definition(my_grammar const&)  
        {  
            Main = boost::spirit::classic::str_p("AAA");  
        }  
        boost::spirit::classic::rule<ScannerT, boost::spirit::classic::parser_tag<0> > Main;  
  
        boost::spirit::classic::rule<ScannerT, boost::spirit::classic::parser_tag<0> > const&  
        start() const  
        {  
            return Main;  
        }  
    };  
};  
  
  
int main(int, char*[])  
{  
    my_grammar g;  
    std::string input = "AAA";  
    auto result = boost::spirit::classic::pt_parse<factory_t>(  
        input.c_str(), input.c_str() + input.size(), g,  
        boost::spirit::classic::space_p);  
    return 0;  
}  
```  
Sorry for the inconvenience.  
Regards,

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-01-21 20:42:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/639#issuecomment-764924195  

> Doing a reproducer, I realized it is not C++20 specific. So it is a bug in gcc.  
  
It should be, however your reproducer compiles fine with today's GCC trunk https://godbolt.org/z/KzY13j  
  
> Though my assertion remains valid: ADL will work with unqualified lookup of the template function.  
  
I'm almost confident that it was done that way to support compilers which have ADL issues, and I feel like it is not worth touching unless there is a real problem.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created at: 2021-01-21 21:23:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/639#issuecomment-764950345  

Hi @stac47,  
  
It looks like in the recent history post our internal gcc, there has been a fix for this gcc bug https://gcc.gnu.org/bugzilla/show_bug.cgi?id=98687 In our case we don't ICE, but it seems it affect how using decl works.  
  
I will rebuild a new Amadeus internal gcc, you will tell me tomorrow if it actually fixes this issue with Boost Spirit, and then we can close this bug report.  
  
Cheers,  
Romain

---

## Comment 5

> Username: stac47  
> Created at: 2021-01-22 06:38:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/639#issuecomment-765172386  

Thanks @Romain-Geissler-1A,  
Just tried the new gcc (tc 21.0.16), and it works.  
Closing the issue.  
Thanks all.
