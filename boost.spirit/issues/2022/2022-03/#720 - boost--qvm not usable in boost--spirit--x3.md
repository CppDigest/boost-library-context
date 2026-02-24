# #720 - boost::qvm not usable in boost::spirit::x3 [Closed]

> Username: octopus-prime  
> Created at: 2022-03-27 12:34:11 UTC  
> Updated at: 2022-04-26 12:45:00 UTC  
> Closed at: 2022-04-26 10:09:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/720  

If you try to parse  
`boost::qvm::vec<float, 3>`  
you fail with compiler errors:  
  
`error: conversion from 'boost::qvm::vec<float, 3>' to 'boost::spirit::x3::unused_type' is ambiguous`  
  
reason:  
```  
/usr/include/boost/qvm/vec.hpp:25:13: note: candidate function [with R = boost::spirit::x3::unused_type]  
  
            operator R() const  
  
            ^  
  
/usr/include/boost/spirit/home/x3/support/unused.hpp:21:9: note: candidate constructor [with T = boost::qvm::vec<float, 3>]  
  
        unused_type(T const&)  
  
        ^  
```

---

## Comment 1

> Username: octopus-prime  
> Created at: 2022-03-27 21:41:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1080024563  

```  
#include <boost/qvm/vec.hpp>  
#include <boost/spirit/home/x3.hpp>  
  
namespace qvm = boost::qvm;  
namespace x3 = boost::spirit::x3;  
  
using scalar_t = float;  
  
auto const scalar = x3::rule<struct scalar_, scalar_t>{"scalar"} =  
x3::float_;  
  
using vector_t = qvm::vec<scalar_t, 3>;  
  
template <size_t N>  
struct foo {  
	template <typename C>  
	void operator()(C const& ctx) const {  
		x3::_val(ctx).a[N] = x3::_attr(ctx);  
	}  
};  
  
auto const vector = x3::rule<struct vector_, vector_t>{"vector"} =  
x3::lit('<') > scalar [foo<0>()] > x3::lit(',') > scalar [foo<1>()] > x3::lit(',') > scalar [foo<2>()] > x3::lit('>');  
  
int main() {  
    std::string input = "center = <0,0,0>";  
    vector_t result;  
    x3::phrase_parse(input.begin(), input.end(), x3::lit("center") > x3::lit('=') > vector > x3::eoi, x3::space, result);  
    return 0;  
}  
```  
Tip: use clang++ because of g++ 'internal compiler error'

---

## Comment 2

> Username: cppljevans  
> Created at: 2022-03-29 12:27:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1081809151  

Try mimicking for qvm what's done with boost::array in fusion here:  
  
https://www.boost.org/doc/libs/develop/libs/fusion/doc/html/fusion/adapted/boost__array.html  
  
It works for me:  
  
> //copy&pasted from:  
> //  ./qvm-not-usable.1rule-deque.cpp  
> //then just changed deque to boost::array & included the fusion   
> adaptation for boost::array.  
> //===================  
> #include <boost/spirit/home/x3.hpp>  
> #include <boost/fusion/adapted/boost_array.hpp>  
>  
> namespace x3 = boost::spirit::x3;  
> namespace fusion = boost::fusion;  
>  
> using scalar_t = int;  
>  
> auto const scalar =  
> x3::int_;  
> #define VECTOR_N 3  
> int constexpr vector_n=VECTOR_N;  
> #include <boost/array.hpp>  
> using vector_t =  
>   boost::array<scalar_t,vector_n>  
>   ;  
> #include <boost/fusion/adapted/boost_array.hpp>  
> #include <boost/fusion/include/io.hpp>  
> using fusion::operators::operator<<;  
>  
> auto const vector_def  
>   =  x3::lit("center")  
>   >  x3::lit('=')  
>   >  x3::lit('<')  
>   >  scalar  
>   >  x3::lit(',')  
>   >  scalar  
>   >  x3::lit(',')  
>   >  scalar  
>   >  x3::lit('>')  
>   >  x3::eoi  
>   ;  
>  
> auto const vector = x3::rule<struct vector_, vector_t>{"vector"}  
>   =  vector_def  
>   ;  
>  
> #include <iostream>  
> int main() {  
>     std::string input = "center = <1,2,3>";  
>     vector_t result;  
>     auto first=input.begin();  
>     auto last=input.end();  
>     x3::phrase_parse(first, last, vector, x3::space, result);  
>     std::cout<<"result="<<result<<";\n";  
>     return 0;  
> }  
  
On 3/27/2022 4:41 PM, Mike Gresens wrote:  
> |#include <boost/qvm/vec.hpp> #include <boost/spirit/home/x3.hpp>   
> namespace qvm = boost::qvm; namespace x3 = boost::spirit::x3; using   
> scalar_t = float; auto const scalar = x3::rule<struct scalar_,   
> scalar_t>{"scalar"} = x3::float_; using vector_t = qvm::vec<scalar_t,   
> 3>; template <size_t N> struct foo { template <typename C> void   
> operator()(C const& ctx) const { x3::_val(ctx).a[N] = x3::_attr(ctx);   
> } }; auto const vector = x3::rule<struct vector_, vector_t>{"vector"}   
> = x3::lit('<') > scalar [foo<0>()] > x3::lit(',') > scalar [foo<1>()]   
> > x3::lit(',') > scalar [foo<2>()] > x3::lit('>'); int main() {   
> std::string input = "center = <0,0,0>"; vector_t result;   
> x3::phrase_parse(input.begin(), input.end(), x3::lit("center") >   
> x3::lit('=') > vector > x3::eoi, x3::space, result); return 0; } |  
>  
> Tip: use clang++ because of g++ 'internal compiler error'  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/720#issuecomment-1080024563>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA5AIADUABFSGQGUU7TVCDPY5ANCNFSM5RYXHM2Q>.  
> You are receiving this because you are subscribed to this   
> thread.Message ID: ***@***.***>  
>

---

## Comment 3

> Username: cppljevans  
> Created at: 2022-03-29 12:58:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1081839006  

Actually, there not even a need for any rule's in this case. This works   
for me:  
  
     x3::phrase_parse(first, last, vector_def, x3::space, result);  
  
and outputs:  
  
     result=(1 2 3);  
  
On 3/29/2022 7:27 AM, cppljevans wrote:  
> Try mimicking for qvm what's done with boost::array in fusion here:  
>  
> https://www.boost.org/doc/libs/develop/libs/fusion/doc/html/fusion/adapted/boost__array.html  
>  
> It works for me:  
>  
> > //copy&pasted from:  
> > //  ./qvm-not-usable.1rule-deque.cpp  
> > //then just changed deque to boost::array & included the fusion  
> > adaptation for boost::array.  
> > //===================  
> > #include <boost/spirit/home/x3.hpp>  
> > #include <boost/fusion/adapted/boost_array.hpp>  
> >  
> > namespace x3 = boost::spirit::x3;  
> > namespace fusion = boost::fusion;  
> >  
> > using scalar_t = int;  
> >  
> > auto const scalar =  
> > x3::int_;  
> > #define VECTOR_N 3  
> > int constexpr vector_n=VECTOR_N;  
> > #include <boost/array.hpp>  
> > using vector_t =  
> >   boost::array<scalar_t,vector_n>  
> >   ;  
> > #include <boost/fusion/adapted/boost_array.hpp>  
> > #include <boost/fusion/include/io.hpp>  
> > using fusion::operators::operator<<;  
> >  
> > auto const vector_def  
> >   =  x3::lit("center")  
> >   >  x3::lit('=')  
> >   >  x3::lit('<')  
> >   >  scalar  
> >   >  x3::lit(',')  
> >   >  scalar  
> >   >  x3::lit(',')  
> >   >  scalar  
> >   >  x3::lit('>')  
> >   >  x3::eoi  
> >   ;  
> >  
> > auto const vector = x3::rule<struct vector_, vector_t>{"vector"}  
> >   =  vector_def  
> >   ;  
> >  
> > #include <iostream>  
> > int main() {  
> >     std::string input = "center = <1,2,3>";  
> >     vector_t result;  
> >     auto first=input.begin();  
> >     auto last=input.end();  
> >     x3::phrase_parse(first, last, vector, x3::space, result);  
> >     std::cout<<"result="<<result<<";\n";  
> >     return 0;  
> > }  
>  
> On 3/27/2022 4:41 PM, Mike Gresens wrote:  
> > |#include <boost/qvm/vec.hpp> #include <boost/spirit/home/x3.hpp>  
> > namespace qvm = boost::qvm; namespace x3 = boost::spirit::x3; using  
> > scalar_t = float; auto const scalar = x3::rule<struct scalar_,  
> > scalar_t>{"scalar"} = x3::float_; using vector_t = qvm::vec<scalar_t,  
> > 3>; template <size_t N> struct foo { template <typename C> void  
> > operator()(C const& ctx) const { x3::_val(ctx).a[N] = x3::_attr(ctx);  
> > } }; auto const vector = x3::rule<struct vector_, vector_t>{"vector"}  
> > = x3::lit('<') > scalar [foo<0>()] > x3::lit(',') > scalar [foo<1>()]  
> > > x3::lit(',') > scalar [foo<2>()] > x3::lit('>'); int main() {  
> > std::string input = "center = <0,0,0>"; vector_t result;  
> > x3::phrase_parse(input.begin(), input.end(), x3::lit("center") >  
> > x3::lit('=') > vector > x3::eoi, x3::space, result); return 0; } |  
> >  
> > Tip: use clang++ because of g++ 'internal compiler error'  
> >  
> > —  
> > Reply to this email directly, view it on GitHub  
> >   
> <https://github.com/boostorg/spirit/issues/720#issuecomment-1080024563>,  
> > or unsubscribe  
> >   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA5AIADUABFSGQGUU7TVCDPY5ANCNFSM5RYXHM2Q>.  
> > You are receiving this because you are subscribed to this  
> > thread.Message ID: ***@***.***>  
> >  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/720#issuecomment-1081809151>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA7FUNWH5MAUUIXVHXDVCLZMPANCNFSM5RYXHM2Q>.  
> You are receiving this because you are subscribed to this   
> thread.Message ID: ***@***.***>  
>

---

## Comment 4

> Username: octopus-prime  
> Created at: 2022-03-29 15:09:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1081993217  

Thanks for your answers @cppljevans   
  
But your parser produces boost::array and not boost::qvm::vec  
  
Parsing as array is simple...   
just use   
#include <boost/fusion/adapted/std_array.hpp>  
  
But the goal is to produce a boost::qvm::vec

---

## Comment 5

> Username: cppljevans  
> Created at: 2022-03-29 15:16:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1082002326  

But my point was that you could just duplicate the   
fusion/adapted/boost_array.hpp file and just replace boost::array with   
qvm::vec  
  
and rename to adapted_qvm.hpp or something similar.  
  
On 3/29/2022 10:09 AM, Mike Gresens wrote:  
>  
> Thanks for your answers @cppljevans <https://github.com/cppljevans>  
>  
> But your parser produces boost::array and not boost::qvm::vec  
>  
> Parsing as array is simple...  
> just use  
> #include <boost/fusion/adapted/std_array.hpp>  
>  
> But the goal is to produce a boost::qvm::vec  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/720#issuecomment-1081993217>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA5YSG4KH5U4TRDP4UDVCMML3ANCNFSM5RYXHM2Q>.  
> You are receiving this because you were mentioned.Message ID:   
> ***@***.***>  
>

---

## Comment 6

> Username: cppljevans  
> Created at: 2022-03-30 02:44:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1082562123  

@octopus-prime I've started to create a boost/fusion/adapted/qvm_vec directory which is essentially a copy of boost/fusion/adapted/std_array with "semi-obvious" modifications to adapt it to qvm::vec.  It's tedious, but before I go any further, I was wondering if you'd already done that; hence, such efforts on my part would be needless.   
  
Please let us know if you are trying to do that, and if so, please let us know the progress.  
  
TIA.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2022-03-30 17:23:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1083415165  

Hi @cppljevans   
  
no - i did not start any adaptation.  
One look at `<boost/fusion/adapted/boost_array.hpp>` was enough:  
  
```  
#include <boost/fusion/support/config.hpp>  
#include <boost/fusion/adapted/boost_array/array_iterator.hpp>  
#include <boost/fusion/adapted/boost_array/tag_of.hpp>  
#include <boost/fusion/adapted/boost_array/detail/is_view_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/is_sequence_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/category_of_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/begin_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/end_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/size_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/at_impl.hpp>  
#include <boost/fusion/adapted/boost_array/detail/value_at_impl.hpp>  
```  
  
Way to much! I am just a qvm/spirit user.  
I want to use it in a private project.  
I don't want to extend qvm/spirit.  
  
So i am very glad to hear that you want to do the adaptation :-)

---

## Comment 8

> Username: octopus-prime  
> Created at: 2022-03-30 17:32:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1083423500  

Without your adaptation my plan would be  
  
- using std::array in AST  
- using qvm::vref if i need qvm-stuff  
  
It's a lot of boilerplate but should work fine...

---

## Comment 9

> Username: lakeweb  
> Created at: 2022-04-03 14:48:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1086884364  

Hi,  
My thought, use a formal parser. This should be reasonably run time safe. I did not deal with the value type of the qvm elements but that should be no big deal in most applications. It even compiles with the wrong type, below, use:  
using my_vt = qvm::vec<int, 3>;  
and you get conversion warnings.  
It would probably be better to make this work like a container, but I didn't get that far.  
I hope this is helpful, best, Dan.  
  
--------  
  
```  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/qvm.hpp>  
  
namespace x3 = boost::spirit::x3;  
namespace qvm = boost::qvm;  
  
std::string in("1.2,34,4.4; 5,4,2.2;");  
  
template <typename TV>  
struct qvm_v_parse : x3::parser<qvm_v_parse<TV>> {  
    using attribute_type = TV;  
    template<typename It, typename Ctx, typename RCtx, typename Attr>  
    bool parse(It& first, It last, Ctx& ctx, RCtx& rctx, Attr& attr) const {  
        for (size_t i = 0; i < sizeof(TV::a)/sizeof(TV::a[0]); ++i) {  
          x3::parse(first, last, x3::double_ >> -x3::lit(','), attr.a[i]);  
            std::cout << i << std::endl;  
        }  
        return true;  
    }  
};  
  
using my_vt = qvm::vec<double, 3>;  
auto qvm_parse_v3 = qvm_v_parse<my_vt>();  
  
int main() {  
    my_vt tvect{ 0 };  
    auto first = in.begin();  
    std::cout << std::boolalpha << x3::phrase_parse(first, in.end(), qvm_parse_v3 >> ';', x3::space, tvect) << std::endl;  
    for (size_t i = 0; i < 3; ++i)  
        std::cout << tvect.a[i] << ", ";  
    std::cout << std::endl << "cursor at: " << std::string(first, in.end()) << std::endl;  
}  
  
```

---

## Comment 10

> Username: cppljevans  
> Created at: 2022-04-04 11:42:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1087452147  

@octopus-prime , you could use BOOST_FUSION_ADAPT_STRUCT if you're careful to use a 2 element preprocessor sequence, as shown below:  
```c++  
//copy&pasted from:  
//  ./qvm-not-usable.1rule.cpp  
//then added adapt_struct with 2 element arg (type,name)  
//===================  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/include/at_c.hpp>  
  
#include <boost/qvm/vec.hpp>  
  
namespace qvm = boost::qvm;  
namespace x3 = boost::spirit::x3;  
namespace fusion = boost::fusion;  
  
using scalar_t = int;  
  
auto const scalar =   
x3::int_;  
#define VECTOR_N 2  
int constexpr vector_n=VECTOR_N;  
using vector_t =   
  qvm::vec<scalar_t,vector_n>  
  ;  
BOOST_FUSION_ADAPT_STRUCT(vector_t, (scalar_t,a[0]), (scalar_t,a[1]));  
  
#include <boost/fusion/include/io.hpp>  
using fusion::operators::operator<<;  
  
auto const vector_def  
  =  x3::lit("center")   
  >  x3::lit('=')  
  >  x3::lit('<')   
  >  scalar   
  >  x3::lit(',')   
  >  scalar   
  >  x3::lit('>')  
  >  x3::eoi  
  ;  
    
#include <iostream>  
int main() {  
    std::string input = "center = <1,2>";  
    vector_t result;  
    auto first=input.begin();  
    auto last=input.end();  
    x3::phrase_parse(first, last, vector_def, x3::space, result);  
    std::cout<<"result="<<result<<";\n";  
    return 0;  
}  
```  
which, at least for me, produces output:  
`  
clang++ -c -O0 -gdwarf-2 -g3  -std=c++20 -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -DUSE_VARIANT_SUB_1_78_0 -DUSE_IS_SUBSTITUTE_1_78_0 -UUSE_DEBUG -UUSE_UNFOLD_LEFT -UTRANSFORM_ATTRIBUTE_RULE2RHS -fno-diagnostics-show-option  -I/home/evansl/prog_dev/boost.org/boost.replacements/common -I/home/evansl/prog_dev/boost.org/boost.additions    -I/home/evansl/prog_dev/boost.org/1_78_0download/download     -ftemplate-depth=200  qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp -MMD -o c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.o   
clang++    c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.o   -o c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.exe  
c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.exe   
result=(1 2);  
  
Compilation finished at Mon Apr  4 06:34:11  
`

---

## Comment 11

> Username: octopus-prime  
> Created at: 2022-04-04 14:01:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1087596696  

Thank you @lakeweb and @cppljevans !  
  
Looks like you both avoided the critical `x3::rule<struct vector_, vector_t>{"vector"}`.  
But without this rule the expectation_failure would not say 'expected vector' - because no name 'vector' was given...  
  
Instead you see someting like  
`N5boost6spirit2x38sequenceINS2_INS2_INS2_INS2_INS2_INS2_INS1_12literal_charINS0_13char_encoding8standardENS1_11unused_typeEEENS1_16expect_directiveINS1_11real_parserIfNS1_13real_policiesIfEEEEEEEENS8_IS7_EEEESD_EESF_EESD_EESF_EENS8_INS1_10eoi_parserEEEEE  
`  
  
So i tried  
```  
namespace boost::spirit::x3 {  
    template <>  
    struct get_info<decltype(vector_def)> {  
        typedef std::string result_type;  
        std::string operator()(decltype(vector_def) const&) const {  
            return "vector";  
        }  
    };  
}  
```  
without success. Still ugly expectation failures.

---

## Comment 12

> Username: cppljevans  
> Created at: 2022-04-04 14:54:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1087659998  

> Thank you @lakeweb and @cppljevans !  
>   
> Looks like you both avoided the critical `x3::rule<struct vector_, vector_t>{"vector"}`. But without this rule the expectation_failure would not say 'expected vector' - because no name 'vector' was given...  
>   
> Instead you see someting like `N5boost6spirit2x38sequenceINS2_INS2_INS2_INS2_INS2_INS2_INS1_12literal_charINS0_13char_encoding8standardENS1_11unused_typeEEENS1_16expect_directiveINS1_11real_parserIfNS1_13real_policiesIfEEEEEEEENS8_IS7_EEEESD_EESF_EESD_EESF_EENS8_INS1_10eoi_parserEEEEE `  
>   
> So i tried  
>   
> ```  
> namespace boost::spirit::x3 {  
>     template <>  
>     struct get_info<decltype(vector_def)> {  
>         typedef std::string result_type;  
>         std::string operator()(decltype(vector_def) const&) const {  
>             return "vector";  
>         }  
>     };  
> }  
> ```  
>   
> without success. Still ugly expectation failures.  
  
@octopus-prime , the following minor change to previous code, when defined(USE_VECTOR_RULE), does use rule:  
```c++  
//copy&pasted from:  
//  ./qvm-not-usable.1rule.cpp  
//then added adapt_struct with 2 element arg (type,name)  
//===================  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/include/at_c.hpp>  
  
#include <boost/qvm/vec.hpp>  
  
namespace qvm = boost::qvm;  
namespace x3 = boost::spirit::x3;  
namespace fusion = boost::fusion;  
  
using scalar_t = int;  
#define VECTOR_N 2  
int constexpr vector_n=VECTOR_N;  
using vector_t =   
  qvm::vec<scalar_t,vector_n>  
  ;  
BOOST_FUSION_ADAPT_STRUCT(vector_t, (scalar_t,a[0]), (scalar_t,a[1]));  
  
#include <boost/fusion/include/io.hpp>  
using fusion::operators::operator<<;  
  
auto const scalar   
  = x3::int_  
  ;  
auto const vector_def  
  =  x3::lit("center")   
  >  x3::lit('=')  
  >  x3::lit('<')   
  >  scalar   
  >  x3::lit(',')   
  >  scalar   
  >  x3::lit('>')  
  >  x3::eoi  
  ;  
#define USE_VECTOR_RULE  
#ifdef USE_VECTOR_RULE  
auto const vector_ = x3::rule<struct vector_, vector_t>{"vector"} =  
  vector_def  
  ;  
#endif//USE_VECTOR_RULE  
  
#include <iostream>  
int main() {  
  #ifdef USE_VECTOR_RULE  
  {//parse error  
    std::string input = "center = <1>";  
    vector_t result;  
    auto first=input.begin();  
    auto last=input.end();  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
    std::cout<<"result="<<result<<";\n";  
  }  
  #else  
  {//parse success  
    std::string input = "center = <1,2>";  
    vector_t result;  
    auto first=input.begin();  
    auto last=input.end();  
    x3::phrase_parse(first, last, vector_def, x3::space, result);  
    std::cout<<"result="<<result<<";\n";  
  }  
  #endif  
    return 0;  
}  
```  
and when run, with input containing error, produces HUGE error output.  Is this kind of output what you were hoping for?  
```  
-*- mode: compilation; default-directory: "~/prog_dev/boost.org/boost.replacements/examples/realworld/issue720/" -*-  
Compilation started at Mon Apr  4 09:45:18  
  
make -k --always-make SRCdeps.inc=no run  
clang++ -c -O0 -gdwarf-2 -g3  -std=c++20 -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -DUSE_VARIANT_SUB_1_78_0 -DUSE_IS_SUBSTITUTE_1_78_0 -UUSE_DEBUG -UUSE_UNFOLD_LEFT -UTRANSFORM_ATTRIBUTE_RULE2RHS -fno-diagnostics-show-option  -I/home/evansl/prog_dev/boost.org/boost.replacements/common -I/home/evansl/prog_dev/boost.org/boost.additions    -I/home/evansl/prog_dev/boost.org/1_78_0download/download     -ftemplate-depth=200  qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp -MMD -o c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.o   
In file included from qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:6:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3.hpp:19:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator.hpp:10:  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:33:56: error: conversion from 'boost::qvm::vec<int, 2>' to 'boost::spirit::x3::unused_type' is ambiguous  
            if (this->left.parse(first, last, context, rcontext, unused)  
                                                       ^~~~~~~~  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:33:28: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
            if (this->left.parse(first, last, context, rcontext, unused)  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:211:26: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            bool r = rhs.parse(  
                         ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:267:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:281:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(rhs, first, last, context, rcontext, attr);  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:330:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/rule.hpp:62:19: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::call_rule_definition<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, mpl_::bool_<false>>' requested here  
                ::call_rule_definition(  
                  ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:119:31: note: in instantiation of function template specialization 'boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        bool r = as_parser(p).parse(first, last, skipper_ctx, unused, attr);  
                              ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:136:16: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse_main<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
        return phrase_parse_main(first, last, p, s, attr, post_skip);  
               ^  
qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:55:9: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/boost.replacements/common/boost/spirit/home/x3/support/unused.hpp:22:9: note: candidate constructor [with T = boost::qvm::vec<int, 2>]  
        unused_type(T const&)  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/qvm/vec.hpp:21:5: note: candidate function [with R = boost::spirit::x3::unused_type]  
    operator R() const  
    ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/string/literal_string.hpp:42:48: note: passing argument to parameter here  
          , Context const& context, unused_type, Attribute_& attr) const  
                                               ^  
In file included from qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:6:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3.hpp:14:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary.hpp:13:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary/guard.hpp:11:  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/directive/expect.hpp:54:64: error: conversion from 'boost::qvm::vec<int, 2>' to 'boost::spirit::x3::unused_type' is ambiguous  
            bool r = this->subject.parse(first, last, context, rcontext, attr);  
                                                               ^~~~~~~~  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:34:32: note: in instantiation of function template specialization 'boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, const boost::spirit::x3::unused_type>' requested here  
                && this->right.parse(first, last, context, rcontext, unused))  
                               ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, boost::qvm::vec<int, 2>, 1>>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:211:26: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            bool r = rhs.parse(  
                         ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:267:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:281:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(rhs, first, last, context, rcontext, attr);  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:330:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/rule.hpp:62:19: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::call_rule_definition<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, mpl_::bool_<false>>' requested here  
                ::call_rule_definition(  
                  ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:119:31: note: in instantiation of function template specialization 'boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        bool r = as_parser(p).parse(first, last, skipper_ctx, unused, attr);  
                              ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:136:16: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse_main<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
        return phrase_parse_main(first, last, p, s, attr, post_skip);  
               ^  
qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:55:9: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/boost.replacements/common/boost/spirit/home/x3/support/unused.hpp:22:9: note: candidate constructor [with T = boost::qvm::vec<int, 2>]  
        unused_type(T const&)  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/qvm/vec.hpp:21:5: note: candidate function [with R = boost::spirit::x3::unused_type]  
    operator R() const  
    ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/char/char_parser.hpp:26:48: note: passing argument to parameter here  
          , Context const& context, unused_type, Attribute& attr) const  
                                               ^  
In file included from qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:6:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3.hpp:14:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary.hpp:13:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary/guard.hpp:11:  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/directive/expect.hpp:54:64: error: conversion from 'boost::qvm::vec<int, 2>' to 'boost::spirit::x3::unused_type' is ambiguous  
            bool r = this->subject.parse(first, last, context, rcontext, attr);  
                                                               ^~~~~~~~  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:253:29: note: in instantiation of function template specialization 'boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, int>' requested here  
            && parser.right.parse(first, last, context, rcontext, r_attr))  
                            ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:211:26: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            bool r = rhs.parse(  
                         ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:267:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:281:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(rhs, first, last, context, rcontext, attr);  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:330:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/rule.hpp:62:19: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::call_rule_definition<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, mpl_::bool_<false>>' requested here  
                ::call_rule_definition(  
                  ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:119:31: note: in instantiation of function template specialization 'boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        bool r = as_parser(p).parse(first, last, skipper_ctx, unused, attr);  
                              ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:136:16: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse_main<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
        return phrase_parse_main(first, last, p, s, attr, post_skip);  
               ^  
qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:55:9: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/boost.replacements/common/boost/spirit/home/x3/support/unused.hpp:22:9: note: candidate constructor [with T = boost::qvm::vec<int, 2>]  
        unused_type(T const&)  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/qvm/vec.hpp:21:5: note: candidate function [with R = boost::spirit::x3::unused_type]  
    operator R() const  
    ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/numeric/int.hpp:35:48: note: passing argument to parameter here  
          , Context const& context, unused_type, Attribute& attr) const  
                                               ^  
In file included from qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:6:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3.hpp:14:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary.hpp:13:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary/guard.hpp:11:  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/directive/expect.hpp:54:64: error: conversion from 'boost::qvm::vec<int, 2>' to 'boost::spirit::x3::unused_type' is ambiguous  
            bool r = this->subject.parse(first, last, context, rcontext, attr);  
                                                               ^~~~~~~~  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:253:29: note: in instantiation of function template specialization 'boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::spirit::x3::unused_type>' requested here  
            && parser.right.parse(first, last, context, rcontext, r_attr))  
                            ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:252:25: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
        if (parser.left.parse(first, last, context, rcontext, l_attr)  
                        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:211:26: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            bool r = rhs.parse(  
                         ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:267:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:281:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(rhs, first, last, context, rcontext, attr);  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:330:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/rule.hpp:62:19: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::call_rule_definition<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, mpl_::bool_<false>>' requested here  
                ::call_rule_definition(  
                  ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:119:31: note: in instantiation of function template specialization 'boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        bool r = as_parser(p).parse(first, last, skipper_ctx, unused, attr);  
                              ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:136:16: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse_main<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
        return phrase_parse_main(first, last, p, s, attr, post_skip);  
               ^  
qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:55:9: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/boost.replacements/common/boost/spirit/home/x3/support/unused.hpp:22:9: note: candidate constructor [with T = boost::qvm::vec<int, 2>]  
        unused_type(T const&)  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/qvm/vec.hpp:21:5: note: candidate function [with R = boost::spirit::x3::unused_type]  
    operator R() const  
    ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/char/char_parser.hpp:26:48: note: passing argument to parameter here  
          , Context const& context, unused_type, Attribute& attr) const  
                                               ^  
In file included from qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:6:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3.hpp:14:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary.hpp:13:  
In file included from C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary/guard.hpp:11:  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/directive/expect.hpp:54:64: error: conversion from 'boost::qvm::vec<int, 2>' to 'boost::spirit::x3::unused_type' is ambiguous  
            bool r = this->subject.parse(first, last, context, rcontext, attr);  
                                                               ^~~~~~~~  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/detail/sequence.hpp:253:29: note: in instantiation of function template specialization 'boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::spirit::x3::unused_type>' requested here  
            && parser.right.parse(first, last, context, rcontext, r_attr))  
                            ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/operator/sequence.hpp:46:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::parse_sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>, boost::spirit::x3::traits::tuple_attribute>' requested here  
            return detail::parse_sequence(*this, first, last, context, rcontext, attr  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:211:26: note: in instantiation of function template specialization 'boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            bool r = rhs.parse(  
                         ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:267:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:281:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs_main<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
            return parse_rhs_main(rhs, first, last, context, rcontext, attr);  
                   ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/detail/rule.hpp:330:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::parse_rhs<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, boost::qvm::vec<int, 2>>' requested here  
                ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
                           ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/nonterminal/rule.hpp:62:19: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::qvm::vec<int, 2>, vector_, true>::call_rule_definition<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>, mpl_::bool_<false>>' requested here  
                ::call_rule_definition(  
                  ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:119:31: note: in instantiation of function template specialization 'boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>>, boost::qvm::vec<int, 2>>' requested here  
        bool r = as_parser(p).parse(first, last, skipper_ctx, unused, attr);  
                              ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/core/parse.hpp:136:16: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse_main<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
        return phrase_parse_main(first, last, p, s, attr, post_skip);  
               ^  
qvm-not-usable.1rule-ADAPT_STRUCT_QVM.cpp:55:9: note: in instantiation of function template specialization 'boost::spirit::x3::phrase_parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::rule_definition<vector_, boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::int_parser<int, 10, 1, -1>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>>, boost::qvm::vec<int, 2>, false, true>, boost::spirit::x3::char_class<boost::spirit::char_encoding::standard, boost::spirit::x3::space_tag>, boost::qvm::vec<int, 2>>' requested here  
    x3::phrase_parse(first, last, vector_, x3::space, result);  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/boost.replacements/common/boost/spirit/home/x3/support/unused.hpp:22:9: note: candidate constructor [with T = boost::qvm::vec<int, 2>]  
        unused_type(T const&)  
        ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/qvm/vec.hpp:21:5: note: candidate function [with R = boost::spirit::x3::unused_type]  
    operator R() const  
    ^  
C:/msys64/home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/auxiliary/eoi.hpp:24:48: note: passing argument to parameter here  
          , Context const& context, unused_type, Attribute&) const  
                                               ^  
5 errors generated.  
make: *** [/home/evansl/prog_dev/root.imk:165: c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue720-78/qvm-not-usable.1rule-ADAPT_STRUCT_QVM.o] Error 1  
make: Target 'run' not remade because of errors.  
  
Compilation exited abnormally with code 2 at Mon Apr  4 09:45:20  
  
```

---

## Comment 13

> Username: octopus-prime  
> Created at: 2022-04-04 15:11:35 UTC  
> Updated at: 2022-04-04 15:25:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1087683229  

These are the compiler erros i opened this issue for...  
  
And if you want to handle `expectation_failure`  
you will need a try/catch, e.g.  
  
```  
try {  
...  
}  
catch (x3::expectation_failure<decltype(first)> const& e) {  
    	std::cerr << e.which() << std::endl;  
}  
```

---

## Comment 14

> Username: cppljevans  
> Created at: 2022-04-24 21:57:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1107925768  

>   
This ambiguous conversion error is eliminated if:  
  
  boost/qvm/vec.hpp  
    
is changed to contain:  
```c++  
#include <boost/spirit/home/x3/support/unused.hpp>  
#include <type_traits>  
  
namespace boost { namespace qvm {  
  
template <class T,int D>  
struct  
vec  
    {  
    T a[D];  
      template   
      < class R  
      , typename Enable=  
        std::enable_if_t  
        < !std::is_same_v<R,boost::spirit::x3::unused_type>  
        >//don't allow conversion to boost::spirit::x3::unused_type  
         //let unused_type to handle that.  
      >  
    operator R() const  
        {  
        R r;  
        assign(r,*this);  
        return r;  
        }  
    };  
.  
.  
.  
}}//boost::qvm  
```  
IOW, the "convenient" conversion operators lead to this "annoyingly inconvenient" error.

---

## Comment 15

> Username: cppljevans  
> Created at: 2022-04-25 16:31:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1108791532  

OTOH, instead of https://github.com/boostorg/spirit/issues/720#issuecomment-1107925768,   
the primitive parsers, such as those for char and int, could be  
changed by providing another template arg, IgnoreContext, to their  
parsers functions, and then the ignore_type conversion operator would  
not then come into play.  
  
To be more specific, here's an example of the change in one primitive  
parser:  
  
```  
diff /home/evansl/prog_dev/boost.org/boost.replacements/issue720/x3_convert/boost/spirit/home/x3/char/char_parser.hpp\  
             /home/evansl/prog_dev/boost.org/1_78_0download/download/boost/spirit/home/x3/char/char_parser.hpp  
23c23  
<         template <typename Iterator, typename Context, typename IgnoreContext, typename Attribute>  
---  
>         template <typename Iterator, typename Context, typename Attribute>  
26c26  
<           , Context const& context, IgnoreContext, Attribute& attr) const  
---  
>           , Context const& context, unused_type, Attribute& attr) const  
  
```  
  
where, obviously, in the above, /home/evansl/prog_dev/boost.org/boost.replacements  
could be replaced by a directory of your choice, and likewise for  
/home/evansl/prog_dev/boost.org/1_78_0download/download.  
  
Similar changes could be made to other primitive parsers.  
  
In short, by simply adding another template parameter, IgnoreContext, to the parse  
function, it all allows any value, not just unused_type, to be  
ignored.  
  
@djowel , that seems a simple solution.  Is there some downside to such a  
solution?

---

## Comment 16

> Username: djowel  
> Created at: 2022-04-25 23:10:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1109126302  

> @djowel , that seems a simple solution. Is there some downside to such a  
> solution?  
  
Pardon, I've not been actively following this thread. Does this mean changing all primitive parsers?

---

## Comment 17

> Username: cppljevans  
> Created at: 2022-04-25 23:16:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1109129866  

I think so.  The problem is that the primitive parsers have   
'unused_type' as the type of one of the arguments,  
  
and unused_type has conversion operator from anything to unused_type.     
Unfortunately, qvm::vec also  
  
has a conversion operator that can convert qvm::vec to unused_type.    
This leads to the compiler error  
  
about ambiguous conversions.  The easiest workaround is as I described   
in my previous comment.  
  
On 4/25/2022 6:11 PM, Joel de Guzman wrote:  
>  
>     @djowel <https://github.com/djowel> , that seems a simple  
>     solution. Is there some downside to such a  
>     solution?  
>  
> Pardon, I've not been actively following this thread. Does this mean   
> changing all primitive parsers?  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/720#issuecomment-1109126302>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBAZVST6CLWTSSLO76VDVG4RATANCNFSM5RYXHM2Q>.  
> You are receiving this because you were mentioned.Message ID:   
> ***@***.***>  
>

---

## Comment 18

> Username: djowel  
> Created at: 2022-04-26 00:00:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1109152725  

Isn't this an issue of `qvm::vec` ? I mean accepting unused_type is entirely different from a conversion operator that can convert to `unused_type`, which I find dubious, no?

---

## Comment 19

> Username: octopus-prime  
> Created at: 2022-04-26 10:09:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1109608142  

The qvm team was working on this issue -> https://github.com/boostorg/qvm/issues/39  
So no changes needed in spirit for this issue.  
So i will close this issue here.

---

## Comment 20

> Username: djowel  
> Created at: 2022-04-26 12:45:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/720#issuecomment-1109751168  

Thanks @octopus-prime !
