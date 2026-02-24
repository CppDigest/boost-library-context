# #746 - apparent uselessness of as_spirit_parser(...)? [Closed]

> Username: cppljevans  
> Created at: 2022-12-04 23:20:56 UTC  
> Updated at: 2025-05-09 23:22:06 UTC  
> Closed at: 2025-05-09 23:19:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/746  

Since decltype(as_spirit_parser(...)) is always void, what's the purpose of using it here:  
  
  https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/core/parser.hpp#L113  
  
instead of the clearer `void`?  
  
Unless, of course, as_spirit_parser is considered a "customization point".  However, it's under  
detail, and IIUC, detail is not intended for customization by the end user.  And even if it were,  
this customization point should be obvious from the end user documentation.  
However, there's nothing here:  
  
  https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/index.html  
  
mentioning customization points.  I'd suggest adding a topic entitled "Customization Points"  
which highlights all places where the user has the ability to customize.   
  
In addition, there should be an example and test demonstrating this as_spirit_parser customization point.

---

## Comment 1

> Username: djowel  
> Created at: 2022-12-05 01:05:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1336587746  

Well, there are such things as private customization points.

---

## Comment 2

> Username: djowel  
> Created at: 2022-12-05 01:21:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1336596183  

> mentioning customization points. I'd suggest adding a topic entitled "Customization Points" which highlights all places where the user has the ability to customize.  
>   
> In addition, there should be an example and test demonstrating this as_spirit_parser customization point.  
  
Agreed 100% Would you like to work on it?

---

## Comment 3

> Username: cppljevans  
> Created at: 2022-12-11 21:02:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1345655737  

> > mentioning customization points. I'd suggest adding a topic entitled "Customization Points" which highlights all places where the user has the ability to customize.  
> > In addition, there should be an example and test demonstrating this as_spirit_parser customization point.  
>   
> Agreed 100% Would you like to work on it?  
  
Hi, Joel.  
  
I tried to create an example; however, I discovered that there's a  
file #inclusion problem.  The problem is:  
  
1) In order for the deduce_as_parser<T,R> class template in parser.hpp  
to see the user's customized as_spirit_parser function, that customized  
as_spirit_parser function must be declared before #include of  
parser.hpp.  
  
2) For the user's customized as_spirit_parser function to use anything  
other than just primitive parsers, it must #include, at least  
indirectly, parser.hpp.  For example, sequence.hpp #include's  
parser.hpp.  
  
Hence, I can't see much use for the as_spirit_parser customization  
point.  
  
Source code illustrating this could be put in a gist or copy and  
pasted to a subsequent comment if the above explanation is  
insufficient.  
  
-regards,  
Larry

---

## Comment 4

> Username: djowel  
> Created at: 2022-12-11 23:42:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1345692172  

You just need forward declarations to break the cycle, no?

---

## Comment 5

> Username: cppljevans  
> Created at: 2022-12-12 00:09:56 UTC  
> Updated at: 2022-12-12 00:17:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1345700812  

> You just need forward declarations to break the cycle, no?  
  
You'd need to forward declare the result type of the as_spirit_parser customization, which is the type of some,  
presumably, compound parser (e.g. sequence) which requires sequence.hpp which requires parser.hpp.  
  
Let me just post code here and let you try to figure it out.  I've failed, so far.  
  
The parser.hpp code (modified for debug output):  
```c++  
/*=============================================================================  
    Copyright (c) 2001-2014 Joel de Guzman  
    Copyright (c) 2013 Agustin Berge  
  
    Distributed under the Boost Software License, Version 1.0. (See accompanying  
    file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
==============================================================================*/  
#if !defined(BOOST_SPIRIT_X3_PARSER_OCTOBER_16_2008_0254PM)  
#define BOOST_SPIRIT_X3_PARSER_OCTOBER_16_2008_0254PM  
  
#include <boost/mpl/bool.hpp>  
#include <boost/type_traits/is_base_of.hpp>  
#include <boost/type_traits/remove_cv.hpp>  
#include <boost/type_traits/remove_reference.hpp>  
#include <boost/utility/declval.hpp>  
#include <boost/utility/enable_if.hpp>  
#include <boost/spirit/home/x3/support/unused.hpp>  
#include <boost/spirit/home/x3/support/context.hpp>  
#include <boost/spirit/home/x3/support/traits/has_attribute.hpp>  
#include <boost/core/ignore_unused.hpp>  
#include <boost/assert.hpp>  
#include <string>  
  
#if !defined(BOOST_SPIRIT_X3_NO_RTTI)  
#include <typeinfo>  
#endif  
#pragma push_macro("FILE_SHORT")  
#define FILE_SHORT "debug/*/core/parser.hpp"  
  
namespace boost { namespace spirit { namespace x3  
{  
    template <typename Subject, typename Action>  
    struct action;  
  
    template <typename Subject, typename Handler>  
    struct guard;  
  
    struct parser_base {};  
    struct parser_id;  
  
    template <typename Derived>  
    struct parser : parser_base  
    {  
        typedef Derived derived_type;  
        static bool const handles_container = false;  
        static bool const is_pass_through_unary = false;  
        static bool const has_action = false;  
  
        constexpr Derived const& derived() const  
        {  
            return *static_cast<Derived const*>(this);  
        }  
  
        template <typename Action>  
        constexpr action<Derived, Action> operator[](Action f) const  
        {  
            return { this->derived(), f };  
        }  
  
        template <typename Handler>  
        constexpr guard<Derived, Handler> on_error(Handler f) const  
        {  
            return { this->derived(), f };  
        }  
    };  
  
    struct unary_category;  
    struct binary_category;  
  
    template <typename Subject, typename Derived>  
    struct unary_parser : parser<Derived>  
    {  
        typedef unary_category category;  
        typedef Subject subject_type;  
        static bool const has_action = Subject::has_action;  
  
        constexpr unary_parser(Subject const& subject)  
            : subject(subject) {}  
  
        unary_parser const& get_unary() const { return *this; }  
  
        Subject subject;  
    };  
  
    template <typename Left, typename Right, typename Derived>  
    struct binary_parser : parser<Derived>  
    {  
        typedef binary_category category;  
        typedef Left left_type;  
        typedef Right right_type;  
        static bool const has_action =  
            left_type::has_action || right_type::has_action;  
  
        constexpr binary_parser(Left const& left, Right const& right)  
            : left(left), right(right) {}  
  
        binary_parser const& get_binary() const { return *this; }  
  
        Left left;  
        Right right;  
    };  
  
    ///////////////////////////////////////////////////////////////////////////  
    // as_parser: convert a type, T, into a parser.  
    ///////////////////////////////////////////////////////////////////////////  
    namespace extension  
    {  
        namespace detail  
        {  
            namespace as_parser_guard  
            {  
                struct undefined_spirit_parser{};  
                  
                constexpr undefined_spirit_parser as_spirit_parser(...)  
                  //Customization point.  
                  //The customization should return not undefined_spirit_parser,  
                  //but a parser.  
                  { return undefined_spirit_parser();}  
  
                template  
                < typename T  
                , typename R =  
                  decltype  
                  ( as_spirit_parser  
                    ( boost::declval<T const&>()  
                    )  
                  )  
                >  
                struct deduce_as_parser  
                {  
                    typedef R type;  
                    typedef typename  
                        boost::remove_cv  
                        < typename boost::remove_reference<R>::type  
                        >::type  
                    value_type;  
  
                    static type call(T const& v)  
                    {  
                        std::cout<<  
                            stringify  
                            ( FILE_SHORT  
                            , ':'  
                            , __LINE__  
                            , ':'  
                            , "deduce_as_parser<T,R>::"  
                            , __func__  
                            , "(T const& v)"  
                            )  
                          ;  
                        std::cout<<"\n:T="<<demangle_fmt_type<T>()<<";\n";  
                        std::cout<<"\n:type="<<demangle_fmt_type<type>()<<";\n";  
                        return as_spirit_parser(v);  
                    }  
                };  
                template<typename T>  
                struct deduce_as_parser<T, void>  
                {  
                    using type=void;  
                    static type call(T const& v)  
                    {  
                        std::cout<<FILE_SHORT<<':'<<__LINE__<<":deduce_as_parser<T,void>::call_trace"<<";\n";  
                        std::cout<<":T="<<demangle_fmt_type<T>()<<";\n";  
                    }  
                };  
            }  
            using as_parser_guard::deduce_as_parser;  
        }  
  
        template <typename T, typename Enable = void>  
        struct as_parser   
        : detail::deduce_as_parser<T>   
        {  
        };  
  
        template <>  
        struct as_parser<unused_type>  
        {  
            typedef unused_type type;  
            typedef unused_type value_type;  
            static constexpr type call(unused_type)  
            {  
                return unused_type();  
            }  
        };  
  
        template <typename Derived>  
        struct as_parser  
          < Derived  
          , typename enable_if  
            < is_base_of  
              < parser_base  
              , Derived  
              >  
            >::type  
          >  
        {  
            typedef Derived const& type;  
            typedef Derived value_type;  
            static constexpr type call(Derived const& p)  
            {  
                return p;  
            }  
        };  
  
    }//extension namespace  
  
    template <typename T>  
    auto  
    as_parser(T const& x)  
    {  
        using ext_as_parser_T=typename extension::as_parser<T>;  
        using ext_as_parser_T_type=typename ext_as_parser_T::type;  
        boost::trace_scope ts  
          ( stringify  
            ( FILE_SHORT  
            , ':'  
            , __LINE__  
            )  
          );  
        std::cout  
          <<"\n:T="<<demangle_fmt_type<T>()  
          <<"\n:ext_as_parser_T="<<demangle_fmt_type<ext_as_parser_T>()  
          <<"\n:ext_as_parser_T_type="<<demangle_fmt_type<ext_as_parser_T_type>()  
          <<";\n";  
        ext_as_parser_T_type ext_as_parser_call_x=ext_as_parser_T::call(x);  
        return ext_as_parser_call_x;  
    }  
  
    template <typename Derived>  
    constexpr Derived const&  
    as_parser(parser<Derived> const& p)  
    {  
        std::cout<<FILE_SHORT<<':'<<__LINE__<<":as_parser_trace(parser<Derived> const& p)"<<";\n";  
        return p.derived();  
    }  
  
    ///////////////////////////////////////////////////////////////////////////  
    // The main what function  
    //  
    // Note: unlike Spirit2, spirit parsers are no longer required to have a  
    // "what" member function. In X3, we specialize the get_info struct  
    // below where needed. If a specialization is not provided, the default  
    // below will be used. The default "what" result will be the typeid  
    // name of the parser if BOOST_SPIRIT_X3_NO_RTTI is not defined, otherwise  
    // "undefined"  
    ///////////////////////////////////////////////////////////////////////////  
    template <typename Parser, typename Enable = void>  
    struct get_info  
    {  
        typedef std::string result_type;  
        std::string operator()(Parser const&) const  
        {  
#if !defined(BOOST_SPIRIT_X3_NO_RTTI)  
            return typeid(Parser).name();  
#else  
            return "undefined";  
#endif  
        }  
    };  
  
    template <typename Parser>  
    std::string what(Parser const& p)  
    {  
        return get_info<Parser>()(p);  
    }  
}}}  
  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
    template <typename Subject, typename Derived, typename Context>  
    struct has_attribute<x3::unary_parser<Subject, Derived>, Context>  
        : has_attribute<Subject, Context> {};  
  
    template <typename Left, typename Right, typename Derived, typename Context>  
    struct has_attribute<x3::binary_parser<Left, Right, Derived>, Context>  
        : mpl::bool_<has_attribute<Left, Context>::value ||  
                has_attribute<Right, Context>::value> {};  
}}}}  
  
  
#pragma pop_macro("FILE_SHORT")  
#endif  
```  
Now, the example code:  
```c++  
//Purpose:  
//  Attempt to provide example of how as_spirit_parser works.  
//  IOW, attempt to do part of what was suggested here:  
/*  
https://github.com/boostorg/spirit/issues/746#issue-1475290123  
 */  
//========  
#include <boost/iostreams/utility/templ_expr/demangle_fmt_type.hpp>  
#include <boost/utility/trace_scope.hpp>  
template< typename T>  
struct as_spir_parser_arg  
/**@brief  
 *  protects auto conversions to T.  
 */  
{  
  explicit as_spir_parser_arg(T t)  
    : _t(t)  
    {}  
  T _t;  
};  
  
#define AS_SPIRIT_PARSER_FWD  
#ifdef AS_SPIRIT_PARSER_FWD  
 namespace boost  
{namespace spirit  
{namespace x3  
{namespace extension  
{namespace detail  
{namespace as_parser_guard  
{  
   struct defined_spirit_parser{}  
     ;  
   defined_spirit_parser as_spirit_parser(as_spir_parser_arg<char> a)  
   /**@brief  
    *  provide dummy result which does not need core/parser.hpp #include'd.  
    */  
     { return defined_spirit_parser();}  
}}}}}}  
#endif//AS_SPIRIT_PARSER_FWD  
  
#include <boost/spirit/home/x3.hpp>  
#include <string>  
#pragma push_macro("FILE_SHORT")  
#define FILE_SHORT "test/as_spirit_parser.cpp"  
  
#ifndef AS_SPIRIT_PARSER_FWD  
 namespace boost  
{namespace spirit  
{namespace x3  
{namespace extension  
{namespace detail  
{namespace as_parser_guard  
{  
  auto as_spirit_parser(as_spir_parser_arg<char> a)  
  /**@brief  
   *  Example of "customization point".  
   *  However, since this is not seen by   
   *  deduce_as_parser within parser.hpp,  
   *  it's not returned by deduce_as_parser::call.  
   */  
    { char x=a._t  
    ; return char_(x)>>char_(x+1)  
    ;}   
}}}}}}  
#endif//#ifdef AS_SPIRIT_PARSER_FWD  
  
using namespace boost::spirit::x3::extension::detail::as_parser_guard;  
  template  
  < typename T  
  >  
void show_as_spirit_parser_type()  
  {  
  ; boost::trace_scope ts(stringify(FILE_SHORT,':',__func__))  
  ; std::cout<<":T="<<demangle_fmt_type<T>()<<";\n"  
  ; T t  
  ; using as_spir_parser_arg_T=as_spir_parser_arg<T>  
  ; std::cout<<":as_spir_parser_arg_T="<<demangle_fmt_type<as_spir_parser_arg_T>()<<";\n"  
  ; as_spir_parser_arg_T arg(t)  
  ; using as_spirit_parser_t=decltype(as_spirit_parser(arg))  
  ; std::cout<<":as_spirit_parser_t="<<demangle_fmt_type<as_spirit_parser_t>()<<";\n"  
  ; using ext_as_parser_T=typename boost::spirit::x3::extension::as_parser<as_spir_parser_arg_T>  
  ; std::cout<<":ext_as_parser_T="<<demangle_fmt_type<ext_as_parser_T>()<<";\n"  
  ; auto as_parser_arg=boost::spirit::x3::as_parser(arg)  
  ; std::cout<<":as_parser_arg="<<demangle_fmt_type(as_parser_arg)<<";\n"  
  ;}  
int main()  
  {  
      boost::iostreams::indent_scoped_ostreambuf<char>  
    indent_outbuf(std::cout,2);  
    std::cout<<std::boolalpha;  
  ; std::cout<<"defined(AS_SPIRIT_PARSER_FWD)="  
  #ifdef AS_SPIRIT_PARSER_FWD  
      <<true  
  #else   
      <<false       
  #endif//AS_SPIRIT_PARSER_FWD  
      <<";\n"  
  ; show_as_spirit_parser_type<char>()  
  ; return 0  
  ;}  
   
#pragma pop_macro("FILE_SHORT")  
```  
Run with defined(AS_SPIRIT_PARSER_FWD), then without, and see if that clarifies things.  
HTH.  
  
P.S.  I'm using functions trace_scope and demangle_fmt_type to provide more readable output.  
That code is located [here](https://github.com/cppljevans/boost.exploratory/tree/main/boost.additions).

---

## Comment 6

> Username: cppljevans  
> Created at: 2022-12-12 00:26:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-1345709225  

To further clarify what the above code shows, I've shown the output with and without defined(AS_SPIRIT_PARSER_FWD).  
defined(AS_SPIRIT_PARSER_FWD):  
```  
-*- mode: compilation; default-directory: "~/prog_dev/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3/" -*-  
Compilation started at Sun Dec 11 14:46:11  
  
make -k --always-make run  
clang++ -c -O0 -g -ggdb -ftemplate-backtrace-limit=0  -std=c++2b -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.additions -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.replacements/as_spirit_parser -I/home/evansl/prog_dev/boost.org/1_80_0download/download -DBOOST_NO_CXX98_FUNCTION_BASE -DBOOST_SPIRIT_X3_CORE_PARSER_TRACE     -ftemplate-depth=200  as_spirit_parser-example.cpp -MMD -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.o   
clang++    c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.o   -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.exe  
c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.exe   
defined(AS_SPIRIT_PARSER_FWD)=true;  
===>Enter:{{test/as_spirit_parser.cpp:show_as_spirit_parser_type  
  :T=char;  
  :as_spir_parser_arg_T=as_spir_parser_arg  
  < char  
  >;  
  :as_spirit_parser_t=boost::spirit::x3::extension::detail::as_parser_guard::defined_spirit_parser;  
  :ext_as_parser_T=boost::spirit::x3::extension::as_parser  
  < as_spir_parser_arg  
    < char  
    >  
  , void  
  >;  
  ===>Enter:{{debug/*/core/parser.hpp:218  
  
    :T=as_spir_parser_arg  
    < char  
    >  
    :ext_as_parser_T=boost::spirit::x3::extension::as_parser  
    < as_spir_parser_arg  
      < char  
      >  
    , void  
    >  
    :ext_as_parser_T_type=boost::spirit::x3::extension::detail::as_parser_guard::defined_spirit_parser;  
    debug/*/core/parser.hpp:144:deduce_as_parser<T,R>::call(T const& v)  
    :T=as_spir_parser_arg  
    < char  
    >;  
  
    :type=boost::spirit::x3::extension::detail::as_parser_guard::defined_spirit_parser;  
  ===>Exit_:}}debug/*/core/parser.hpp:218  
  :as_parser_arg=boost::spirit::x3::extension::detail::as_parser_guard::defined_spirit_parser;  
===>Exit_:}}test/as_spirit_parser.cpp:show_as_spirit_parser_type  
  
Compilation finished at Sun Dec 11 14:46:29  
```  
and with !defined(AS_SPIRIT_PARSER_FWD):  
```  
-*- mode: compilation; default-directory: "~/prog_dev/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3/" -*-  
Compilation started at Sun Dec 11 14:28:17  
  
make -k --always-make run  
clang++ -c -O0 -g -ggdb -ftemplate-backtrace-limit=0  -std=c++2b -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.additions -I/home/evansl/prog_dev/boost.org/boost.exploratory/boost.replacements/as_spirit_parser -I/home/evansl/prog_dev/boost.org/1_80_0download/download -DBOOST_NO_CXX98_FUNCTION_BASE -DBOOST_SPIRIT_X3_CORE_PARSER_TRACE     -ftemplate-depth=200  as_spirit_parser-example.cpp -MMD -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.o   
clang++    c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.o   -o c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.exe  
c:/msys64/tmp/build/clangxx14_0_3/boost.org/boost.exploratory/boost.replacements/as_spirit_parser/test/x3-80/as_spirit_parser-example.exe   
defined(AS_SPIRIT_PARSER_FWD)=false;  
===>Enter:{{test/as_spirit_parser.cpp:show_as_spirit_parser_type  
  :T=char;  
  :as_spir_parser_arg_T=as_spir_parser_arg  
  < char  
  >;  
  :as_spirit_parser_t=x3_sequence  
  < boost::spirit::x3::literal_char  
    < boost::spirit::char_encoding::standard  
    , char  
    >  
  , boost::spirit::x3::literal_char  
    < boost::spirit::char_encoding::standard  
    , char  
    >   
  >;  
  :ext_as_parser_T=boost::spirit::x3::extension::as_parser  
  < as_spir_parser_arg  
    < char  
    >  
  , void  
  >;  
  ===>Enter:{{debug/*/core/parser.hpp:218  
  
    :T=as_spir_parser_arg  
    < char  
    >  
    :ext_as_parser_T=boost::spirit::x3::extension::as_parser  
    < as_spir_parser_arg  
      < char  
      >  
    , void  
    >  
    :ext_as_parser_T_type=boost::spirit::x3::extension::detail::as_parser_guard::undefined_spirit_parser;  
    debug/*/core/parser.hpp:144:deduce_as_parser<T,R>::call(T const& v)  
    :T=as_spir_parser_arg  
    < char  
    >;  
  
    :type=boost::spirit::x3::extension::detail::as_parser_guard::undefined_spirit_parser;  
  ===>Exit_:}}debug/*/core/parser.hpp:218  
  :as_parser_arg=boost::spirit::x3::extension::detail::as_parser_guard::undefined_spirit_parser;  
===>Exit_:}}test/as_spirit_parser.cpp:show_as_spirit_parser_type  
  
Compilation finished at Sun Dec 11 14:28:34  
```

---

## Comment 7

> Username: saki7  
> Created at: 2025-05-09 23:19:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/746#issuecomment-2868057549  

Please don't rely on stuffs defined in `namespace detail`. Even if they are seemingly not working, obscure, or somewhat disturbing. There's no chance for us to proceed on a constructive discussion, because they are implementation details.
