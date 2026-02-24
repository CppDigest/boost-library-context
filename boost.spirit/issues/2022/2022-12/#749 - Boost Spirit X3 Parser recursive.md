# #749 - Boost Spirit X3 Parser recursive [Closed]

> Username: Hackman1993  
> Created at: 2022-12-30 16:49:11 UTC  
> Updated at: 2025-05-10 14:48:45 UTC  
> Closed at: 2025-05-09 23:08:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/749  

I'm trying to write a parser to parse html with boost spirit x3, and I wrote parsers below:  
  
Sorry about the long code, I've put them together so you can understand what I'm thinking。 The problem is these code can't compile. Error is :  
  
fatal error C1202: recursive type or function dependency context too complex  
  
I know this error comes out because of my parser html_element_ references tag_block_, and tag_block_ references html_element_, but I don't know how to make it work.  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <iostream>  
using namespace boost::spirit::x3;  
struct tag_name{};  
struct html_tag;  
struct html_comment;  
struct attribute_data : boost::spirit::x3::position_tagged {  
  std::string name;  
  boost::optional<std::string> value;  
};  
  
  
struct tag_header :  boost::spirit::x3::position_tagged {  
  std::string name;  
  std::vector<attribute_data> attributes;  
};  
  
struct self_tag: boost::spirit::x3::position_tagged {  
  tag_header header;  
};  
  
struct html_element : boost::spirit::x3::position_tagged, boost::spirit::x3::variant< std::string, self_tag, boost::recursive_wrapper<html_tag>>{  
  using base_type::base_type;  
  using base_type::operator=;  
};  
  
  
  
struct html_tag: boost::spirit::x3::position_tagged {  
  tag_header header;  
  std::vector<html_element> children;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(attribute_data, name, value);  
BOOST_FUSION_ADAPT_STRUCT(tag_header, name, attributes);  
BOOST_FUSION_ADAPT_STRUCT(self_tag, header);  
BOOST_FUSION_ADAPT_STRUCT(html_tag,header,children);  
  
// These are the attributes parser, seems fine  
struct attribute_parser_id;  
auto attribute_identifier_= rule<attribute_parser_id, std::string>{"AttributeIdentifier"} = lexeme[+(char_ - char_(" /=>"))];  
auto attribute_value_= rule<attribute_parser_id, std::string>{"AttributeValue"} =  
                           lexeme["\"" > +(char_ - char_("\"")) > "\""]|lexeme["'" > +(char_ - char_("'")) > "'"]|  
                           lexeme[+(char_ - char_(" />"))];  
auto single_attribute_ = rule<attribute_parser_id, attribute_data>{"SingleAttribute"} = attribute_identifier_ > -("=">  attribute_value_);  
auto attributes_ = rule<attribute_parser_id, std::vector<attribute_data>>{"Attributes"} = (*single_attribute_);  
  
  
struct tag_parser_id;  
  
  
auto tag_name_begin_func = [](auto &ctx){  
  get<tag_name>(ctx) = _attr(ctx).name;  
  //_val(ctx).header.name = _attr(ctx);  
  std::cout << typeid(_val(ctx)).name() << std::endl;  
  
};  
auto tag_name_end_func = [](auto &ctx){  
  _pass(ctx) = get<tag_name>(ctx) == _attr(ctx);  
};  
  
auto self_tag_name_action = [](auto &ctx){  
  _val(ctx).header.name = _attr(ctx);  
};  
auto self_tag_attribute_action = [](auto &ctx){  
  _val(ctx).header.attributes = _attr(ctx);  
};  
  
auto inner_text = lexeme[+(char_-'<')];  
auto tag_name_ = rule<tag_parser_id, std::string>{"HtmlTagName"} = lexeme[*(char_ - char_(" />"))];  
auto self_tag_ = rule<tag_parser_id, self_tag>{"HtmlSelfTag"} = '<' > tag_name_[self_tag_name_action] > attributes_[self_tag_attribute_action] > "/>";  
auto tag_header_ = rule<tag_parser_id, tag_header>{"HtmlTagBlockHeader"} = '<' > tag_name_ > attributes_ > '>';  
  
rule<tag_parser_id, html_tag> tag_block_;  
  
rule<tag_parser_id, html_element> html_element_ = "HtmlElement";  
  
auto tag_block__def = with<tag_name>(std::string())[tag_header_[tag_name_begin_func] > (*html_element_) > "</" > omit[tag_name_[tag_name_end_func]] > '>'];  
auto html_element__def = inner_text | self_tag_ | tag_block_ ;  
  
BOOST_SPIRIT_DEFINE(tag_block_, html_element_);  
int main()  
{  
  std::string source = "<div data-src=\"https://www.google.com\" id='hello world'></div>";  
  html_element result;  
  auto const parser = html_element_;  
  auto parse_result = phrase_parse(source.begin(), source.end(), parser, ascii::space, result);  
}  
  
```

---

## Comment 1

> Username: djowel  
> Created at: 2022-12-31 00:50:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1368136798  

Study the examples. There are lots of examples with such recursion. You might also want to break things up in smaller units in separate TUs. Again, there are examples you can study.

---

## Comment 2

> Username: Hackman1993  
> Created at: 2022-12-31 02:54:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1368153463  

> Study the examples. There are lots of examples with such recursion. You might also want to break things up in smaller units in separate TUs. Again, there are examples you can study.  
  
Ty for the reply,I've read and try examples in past few days. In X3 examples, parser can be cut in to 3 parts, so reference chain can be A -> B ->C ->A. I thingk that's why the example won't trigger this problem. But in my case, In order to match end tag and begin tag, I have to use with to provide a context so I can do the match later, so I can't cut the parser. That's cause my reference chain like A -> B -> A. I think this is the reason makes templates too complex(if I'm right) . But In my case I don't know how to break it?

---

## Comment 3

> Username: djowel  
> Created at: 2022-12-31 03:09:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1368155241  

Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.

---

## Comment 4

> Username: cppljevans  
> Created at: 2022-12-31 05:39:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1368168668  

> Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.  
  
This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.

---

## Comment 5

> Username: djowel  
> Created at: 2023-01-01 16:19:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1368480359  

> This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.  
  
Wonderful! I was about to give a follow answer on that. You beat me to the punch.

---

## Comment 6

> Username: Hackman1993  
> Created at: 2023-01-04 05:23:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1370502197  

Yeah, thanks to sehe on this part, and you may noticed that semantic actions may broke attribute propagation. Does it designed to be like that or it's a bug?

---

## Comment 7

> Username: cppljevans  
> Created at: 2023-01-04 18:25:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1371272579  

> > Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.  
>   
> This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.  
  
I have to confess I did not try to compile the code, but when I did today, I got a compile error:  
```  
C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/container_traits.hpp:74:56: error: no type named 'value_type' in 'Ast::html_element'  
      : detail::remove_value_const<typename Container::value_type>  
                                   ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
```  
@Hackman1993, were you able to successfully compile [sehe's code](http://coliru.stacked-crooked.com/a/b4de81a919bec94a)?

---

## Comment 8

> Username: cppljevans  
> Created at: 2023-01-04 18:39:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1371286559  

> > > Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.  
> >   
> >   
> > This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.  
>   
> I have to confess I did not try to compile the code, but when I did today, I got a compile error:  
>   
> ```  
> C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/container_traits.hpp:74:56: error: no type named 'value_type' in 'Ast::html_element'  
>       : detail::remove_value_const<typename Container::value_type>  
>                                    ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
> ```  
>   
> @Hackman1993, were you able to successfully compile [sehe's code](http://coliru.stacked-crooked.com/a/b4de81a919bec94a)?  
  
OOPS, never mind.  I was using wrong include flags.  Sorry for noise :(

---

## Comment 9

> Username: Hackman1993  
> Created at: 2023-01-05 00:50:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1371602506  

> > > > Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.  
> > >   
> > >   
> > > This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.  
> >   
> >   
> > I have to confess I did not try to compile the code, but when I did today, I got a compile error:  
> > ```  
> > C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/container_traits.hpp:74:56: error: no type named 'value_type' in 'Ast::html_element'  
> >       : detail::remove_value_const<typename Container::value_type>  
> >                                    ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
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
> > @Hackman1993, were you able to successfully compile [sehe's code](http://coliru.stacked-crooked.com/a/b4de81a919bec94a)?  
>   
> OOPS, never mind. I was using wrong include flags. Sorry for noise :(  
  
Aha, that's always happens.spirit's variant and boost's variant is not equal.

---

## Comment 10

> Username: cppljevans  
> Created at: 2023-01-08 19:30:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1374909804  

> > > > > Take a look at the `mini_xml` in the Qi examples. It makes use of inherited attributes, which is alas not available in X3. You might want to just use Qi. It is rather tricky to do in X3 without inherited attributes.  
> > > >   
> > > >   
> > > > This question was also posted on [stack overflow](https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3) where sehe gave his usual excellent answer using the context to store a stack of strings for the tags.  
> > >   
> > >   
> > > I have to confess I did not try to compile the code, but when I did today, I got a compile error:  
> > > ```  
> > > C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/container_traits.hpp:74:56: error: no type named 'value_type' in 'Ast::html_element'  
> > >       : detail::remove_value_const<typename Container::value_type>  
> > >                                    ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
> > > ```  
> > >   
> > >   
> > >       
> > >         
> > >       
> > >   
> > >         
> > >       
> > >   
> > >       
> > >     
> > > @Hackman1993, were you able to successfully compile [sehe's code](http://coliru.stacked-crooked.com/a/b4de81a919bec94a)?  
> >   
> >   
> > OOPS, never mind. I was using wrong include flags. Sorry for noise :(  
>   
> Aha, that's always happens.spirit's variant and boost's variant is not equal.  
  
Hi Hackman1993,  
  
The problem with my code was more involved than just #include'ing the  
wrong variant.  I had used code with LOTS of debug print  
modifications, and, apparently, one of those modifications was wrong.  
  
Nevertheless, I've attached a much simplified code which should make  
it easier, I hope, to explore how to avoid using semantic actions.  
You'll soon note that the only primitive parser used in uint_.  IOW,  
there's no strings parsed, the only parsers used  only detect the <X> and <X/> and  
then just Y where all of X and Y are uint_.  There's also debug prints  
from the semantic actions.  
  
Here's the output from the run:  
```  
-*- mode: compilation; default-directory: "~/prog_dev/boost.org/boost.exploratory/boost.replacements/examples/realworld/issue749/" -*-  
Compilation started at Sun Jan  8 13:24:13  
  
make -k --always-make run  
clang++ -c -O0 -g -ggdb -ftemplate-backtrace-limit=0  -std=c++2b -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.additions -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.replacements/common  -I/home/evansl/prog_dev/boost.org/1_80_0download/download -DUSE_SEMANTIC_ACTIONS -DUSE_RULE_DEFINITIONS  -DBOOST_NO_CXX98_FUNCTION_BASE -DUSE_SEMANTIC_ACTIONS -DUSE_RULE_DEFINITIONS    -ftemplate-depth=200  coliru_sehe-debug-simple.cpp -MMD -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/examples/realworld/issue749-80/coliru_sehe-debug-simple.o   
clang++    c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/examples/realworld/issue749-80/coliru_sehe-debug-simple.o   -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/examples/realworld/issue749-80/coliru_sehe-debug-simple.exe  
c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/examples/realworld/issue749-80/coliru_sehe-debug-simple.exe   
USE_SEMANTIC_ACTIONS=yes;  
USE_RULE_DEFINITIONS=yes;  
{tag=html_element_ pass;  
{tag=tag_name_begin_func;  
:tag_beg=0;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=0;  
:tag_end=0;  
:tags_matched=true;  
}tag=tag_name_end_func;  
"<0></0>" -> Ok  
{tag=tag_name_begin_func;  
:tag_beg=0;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=0;  
:tag_end=0;  
:tags_matched=true;  
}tag=tag_name_end_func;  
"<0>0</0>" -> Ok  
{tag=tag_name_begin_func;  
:tag_beg=0;  
}tag=tag_name_begin_func;  
{tag=tag_name_begin_func;  
:tag_beg=1;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=1;  
:tag_end=1;  
:tags_matched=true;  
}tag=tag_name_end_func;  
{tag=tag_name_begin_func;  
:tag_beg=2;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=2;  
:tag_end=2;  
:tags_matched=true;  
}tag=tag_name_end_func;  
{tag=tag_name_end_func;  
:tag_beg=0;  
:tag_end=0;  
:tags_matched=true;  
}tag=tag_name_end_func;  
"<0><1>11</1><2>22</2></0>" -> Ok  
}tag=html_element_ pass;  
{tag=html_element_ fail;  
{tag=tag_name_begin_func;  
:tag_beg=0;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=0;  
:tag_end=1;  
:tags_matched=false;  
}tag=tag_name_end_func;  
Fails as expected: "<0></1>"  
{tag=tag_name_begin_func;  
:tag_beg=0;  
}tag=tag_name_begin_func;  
{tag=tag_name_end_func;  
:tag_beg=0;  
:tag_end=1;  
:tags_matched=false;  
}tag=tag_name_end_func;  
Fails as expected: "<0>999</1>"  
}tag=html_element_ fail;  
  
Compilation finished at Sun Jan  8 13:24:16  
```  
and here's the code:  
```  
//OriginalSource:  
//  http://coliru.stacked-crooked.com/a/b4de81a919bec94a  
//LinkedToFrom:  
//  https://stackoverflow.com/questions/74963861/parse-html-with-boost-spirit-x3  
//===============  
#include <iomanip>  
#include <iostream>  
#include <string>  
//#define ISSUE749_EXE_TRACE  
#ifdef ISSUE749_EXE_TRACE  
  #pragma push_macro("FILE_SHORT")  
  #define FILE_SHORT "issue749/coliru_sehe.cpp"  
  #include <boost/iostreams/utility/templ_expr/demangle_fmt_type.hpp>  
  #include <boost/utility/trace_scope.hpp>  
  using boost::trace_scope;  
#else  
  struct trace_scope  
    {  
        std::string tag;  
        trace_scope(std::string tag_)  
          : tag(tag_)  
          {  std::cout<<"{tag="<<tag<<";\n";  
          }  
        ~trace_scope()  
          {  std::cout<<"}tag="<<tag<<";\n";  
          }  
    };  
#endif//ISSUE749_EXE_TRACE  
  
//#define BOOST_SPIRIT_X3_DEBUG  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <stack>  
  
namespace x3 = boost::spirit::x3;  
using namespace std::string_literals;  
  
namespace Ast {  
    struct html_tag;  
  
    struct tag_header {  
        unsigned int     name;  
    };  
  
    using html_element =  
        x3::variant  
        < unsigned int  
        , boost::recursive_wrapper<html_tag>  
        >;  
          
    using html_elements = std::vector<html_element>;  
  
    struct html_tag {  
        tag_header    header;  
        html_elements children;  
    };  
} // namespace Ast  
  
BOOST_FUSION_ADAPT_STRUCT(Ast::tag_header, name)  
BOOST_FUSION_ADAPT_STRUCT(Ast::html_tag, header, children)  
  
namespace Parser {  
      struct   
    tag_stack   
      {};  
  
  //#define USE_RULE_DEFINITIONS  
  //#define USE_SEMANTIC_ACTIONS  
  #ifdef USE_SEMANTIC_ACTIONS  
  
    auto tag_name_begin_func = [](auto& ctx)   
      {   
        trace_scope ts("tag_name_begin_func");  
        auto& tag_stk = get<tag_stack>(ctx);  
        auto& tag_beg=_attr(ctx).name;  
        std::cout<<":tag_beg="<<tag_beg<<";\n";  
        tag_stk.push(tag_beg);  
      };  
  
    auto tag_name_end_func = [](auto& ctx)   
      {  
        trace_scope ts("tag_name_end_func");  
        auto& tag_stk = get<tag_stack>(ctx);  
        auto& tag_beg = tag_stk.top();  
        std::cout<<":tag_beg="<<tag_beg<<";\n";  
        auto tag_end = _attr(ctx);  
        std::cout<<":tag_end="<<tag_end<<";\n";  
        auto tags_matched=tag_beg == tag_end;  
        std::cout<<":tags_matched="<<tags_matched<<";\n";  
        _pass(ctx) = tags_matched;  
        tag_stk.pop();  
      };  
  #endif//USE_SEMANTIC_ACTIONS  
    
      auto   
    tag_name_  
      = x3::rule<struct HtmlTagName_tag, unsigned int>{"HtmlTagName"}  
      = x3::uint_  
      ;  
      auto   
    tag_header_  
    #ifdef USE_RULE_DEFINITIONS  
      = x3::rule<struct HtmlTagBlockHeader_tag, Ast::tag_header>{"HtmlTagBlockHeader"}   
    #endif  
      = '<'   
      >> tag_name_   
      >> '>'  
      ;  
  
      x3::rule<struct tag_block__tag, Ast::html_tag>  
    tag_block_    = "TagBlock"  
      ;  
      x3::rule<struct html_element__tag, Ast::html_element>   
    html_element_ = "HtmlElement"  
      ;  
      auto   
    tag_block__def =  
        tag_header_  
      #ifdef USE_SEMANTIC_ACTIONS  
        [tag_name_begin_func]  
      #endif  
      >> *html_element_   
      >> "</"   
      >> x3::omit  
        [ tag_name_  
        #ifdef USE_SEMANTIC_ACTIONS  
          [tag_name_end_func]  
        #endif  
        ]   
      >> '>'  
      ;  
    auto inner_text        = x3::uint_;  
    auto html_element__def =   
        inner_text   
      | tag_block_  
      ;  
    BOOST_SPIRIT_DEFINE(tag_block_, html_element_)  
}  
  
namespace unit_tests {  
    template <bool ShouldSucceed = true, typename P>  
    void test(P const& rule, std::initializer_list<std::string_view> cases) {  
        auto start=  
        #ifdef USE_SEMANTIC_ACTIONS  
          x3::with<Parser::tag_stack>(std::stack<unsigned int>{})  
          [  
        #endif  
            rule  
        #ifdef USE_SEMANTIC_ACTIONS  
          ]  
        #endif  
          ;        
        for (auto input : cases) {  
            if constexpr (ShouldSucceed) {  
                typename x3::traits::attribute_of<P, x3::unused_type>::type attr_of;  
              #ifdef ISSUE749_EXE_TRACE  
                std::cout<<":attr_of=\n"<<demangle_fmt_type(attr_of)<<";\n";  
              #endif  
                auto ok = phrase_parse(input.begin(), input.end(), start, x3::space, attr_of);  
                std::cout << quoted(input) << " -> " << (ok ? "Ok" : "FAILED") << std::endl;  
            } else {  
                auto ok = phrase_parse(input.begin(), input.end(), start, x3::space);  
                if (!ok)  
                    std::cout << "Fails as expected: " << quoted(input) << std::endl;  
                else  
                    std::cout << "SHOULD HAVE FAILED: " << quoted(input) << std::endl;  
            }  
        }  
    }  
}  
  
int main() {  
  #if 1 && defined(ISSUE749_EXE_TRACE)  
      boost::iostreams::indent_scoped_ostreambuf<char>  
    indent_outbuf(std::cout,2);  
  #endif  
    std::cout<<std::boolalpha;  
    std::cout<<"USE_SEMANTIC_ACTIONS=";  
  #ifdef USE_SEMANTIC_ACTIONS  
    std::cout<<"yes";  
  #else  
    std::cout<<"not";  
  #endif  
    std::cout<<";\n";  
    std::cout<<"USE_RULE_DEFINITIONS=";  
  #ifdef USE_RULE_DEFINITIONS  
    std::cout<<"yes";  
  #else  
    std::cout<<"not";  
  #endif  
    std::cout<<";\n";  
  #if 0  
    {   
        trace_scope ts("tag_name_");  
        unit_tests::test  
        ( Parser::tag_name_  
        , { R"(abc)"  
          , R"(def)"  
          }  
        );  
    }  
  #endif  
  #if 1  
    {  
        trace_scope ts("html_element_ pass");  
        unit_tests::test  
        ( Parser::html_element_  
        , { R"(<0></0>)"  
        #if 1  
          , R"(<0>0</0>)"  
          , R"(<0><1>11</1><2>22</2></0>)"  
        #endif  
          }  
        );  
    }  
  #endif  
  #if 1  
    {  
        trace_scope ts("html_element_ fail");  
        unit_tests::test<false>  
        ( Parser::html_element_  
        , { R"(<0></1>)"  
          , R"(<0>999</1>)"  
          }  
        );  
    }  
  #endif                              
}  
#ifdef ISSUE749_EXE_TRACE  
  #pragma pop_macro("FILE_SHORT")  
#endif//ISSUE749_EXE_TRACE  
  
```  
  
Joel, maybe you could modify the code to avoid the semantic actions?  
  
-Larry

---

## Comment 11

> Username: Hackman1993  
> Created at: 2023-01-09 07:10:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1375190683  

I think it's hard to solve without semantic actions in this problem .In order to match the begin tag, we always have to match the begin tag name, Spirit won't do that for us unless we got a built-in support,. As Joel said , using Spirit Qi is more tricky. but even in  Qi's mini_xml example , Joel still using a semantic action to do that. So I'm going to stick on the semantic action way and stop digging deeper.

---

## Comment 12

> Username: djowel  
> Created at: 2023-01-09 10:35:04 UTC  
> Updated at: 2023-01-09 10:35:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1375407619  

I'm with sehe: just use an html parser. `mini_xml` is just an example. But if you really want to do it using Spirit, I'd just collect the parse-tree structure in the parsing step and do the tag matching in a post processing, semantic analysis step by walking the generated tree from the prior parsing step.

---

## Comment 13

> Username: cppljevans  
> Created at: 2023-01-24 00:13:00 UTC  
> Updated at: 2023-11-16 17:22:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-1401185690  

> I think it's hard to solve without semantic actions in this problem .In order to match the begin tag, we always have to match the begin tag name, Spirit won't do that for us unless we got a built-in support,. As Joel said , using Spirit Qi is more tricky. but even in Qi's mini_xml example , Joel still using a semantic action to do that. So I'm going to stick on the semantic action way and stop digging deeper.  
  
@Hackman1993, the code [here](https://godbolt.org/z/x9G9dGPjG) doesn't use semantic actions.  Instead, it uses new make_transform_parser and transform_attribute_tagged.  transform_attribute_tagged is pretty close to the  
transform_attribute template in existing nonterminal/detail/transform_attribute.hpp.  The main difference  
is that post returns bool instead of void, and it's there that the check is done for equality of beg/end tags.  
  
Or you could use the pattern shown [here](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp) which allows using either semantic action or the new make_transform_parser.hpp.  The former pollutes the Parser namespace, that latter does not.

---

## Comment 14

> Username: saki7  
> Created at: 2025-05-09 23:08:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-2868041568  

Closing as (1) stale, and (2) the official bug tracker of open source projects is not the place for asking questions about your own application's implementation details.

---

## Comment 15

> Username: cppljevans  
> Created at: 2025-05-10 14:48:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/749#issuecomment-2868941394  

> Closing as (1) stale, and (2) the official bug tracker of open source projects is not the place for asking questions about your own application's implementation details.  
  
W.R.T. item (2), I was not asking questions, I was proposing solutions.
