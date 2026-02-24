# #780 - need for "RHS" transform_attribute [Closed]

> Username: cppljevans  
> Created at: 2023-12-08 15:58:57 UTC  
> Updated at: 2025-05-25 16:10:58 UTC  
> Closed at: 2025-05-09 22:32:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/780  

<!DOCTYPE html>  
<html lang="en">  
<head>  
  <title>Need for "RHS" transform_attribute</title>  
</head>  
<body>  
<h1>Existing use of "external" transform_attribute</h1>  
<p>The transform_attribute class is used in 2 places</p>  
<ol>  
  <li><a href="https://github.com/boostorg/spirit/blob/master/include/boost/spirit/home/x3/nonterminal/rule.hpp#L149">within <code>rule::parse  
  function</code></a></li>  
    <p>  
    The purpose here is to transform the passed-in, or <b>external</b>,  
    attribute type, <code>Attribute_</code>, to the  
    rule's attribute, <code>attribute_type</code>.  
    </p>  
  <li><a href="https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L306">within <code>call_rule_definition  
  function</code></a></li>  
    <p>  
    The purpose here is the very same as the purpose above.  The only  
    difference is the name of the types.  Here, instead of the  
    passed-in attribute type being named <code>Attribute_</code>, the name  
    is <code>ActualAttribute</code>, and instead of the rule's attribute  
    being named <code>attribute_type</code>, the  
    name is simply <code>Attribute</code>.  
    </p>  
</ol>  
<h1>Proposed use of "RHS" transform_attribute</h1>  
  
  <p>  
  Currently, the x3 docs require that the rule attribute is  
  "compatible" with the RHS  
  attribute <a href="https://ciere.com/cppnow15/using_x3.pdf">(page  
  96)</a>.  
  </p>  
    
  <p>  
  The propsed <b>RHS</b> transform would relieve the end-user of this  
  compatibility constraint by transforming a possibly incompatible  
  rule attribute to the <b>RHS</b>'s attribute.  
  </p>  
    
  <p>If such a RHS transform_attribute's <code>post</code> returns  
  a <code>bool</code> value, then it could be used to replace some  
  semantic actions and changes to the <a href="https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp#L133"><code>context</code></a>, as  
  shown <a href="https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp#L318">here</a>.  
  </p>  
    
  <p>This RHS transform_attribute use could be placed somewhere after  
  the <b>external</b> transform attribute use, possibly in  
  the <code>call_rule_defintion</code> body just  
  before <code>parse_rhs</code> call <a href="https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L328">here</a>.  
  </p>  
<code>  
  using rhs_attr=  
    typename traits::attribute_of&lt;RHS,Context&gt;::type  
    ;  
  using transform=  
    traits::transform_attribute_tagged  
    &lt; ActualAttribute  
    , rhs_attr  
    , rule_internal_id&lt;ID&gt;  
    &gt;;  
  using transform_type=typename transform::type;  
  transform_type rhs_actual=  
    transform::pre  
    ( attr_  
    );  
  ok_parse = parse_rhs  
    ( rhs  
    , first  
    , last  
    , context  
    , attr_//RContext&  
      //Rule Context, to allow actions to access rule attribute.  
      //see val_context in core/call.hpp.  
    , rhs_actual  
    , has_action_and_not_explicit_propagation_t()  
    );  
  if(ok_parse)  
  {   
      ok_parse=transform::post(attr_,rhs_actual);  
  }  
  
</code>

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-09 22:32:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/780#issuecomment-2867991446  

To be honest I have never encountered difficulty on this area. Attributes are attributes. They sometimes get substituted, composed, or deducted, but you can't expect X3 to magically translate incompatible attributes for your specific rule.   
  
How does this addition solve real issues in practice? I think that correcting your attribute would be sufficient. That kind of fix should be as easy as fixing your template parameters on ordinary classes.  
  
Feel free to reopen this if you have further concerns. If so, please provide a minimal code which illustrates your problem.

---

## Comment 2

> Username: cppljevans  
> Created at: 2025-05-23 01:36:41 UTC  
> Updated at: 2025-05-25 15:39:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/780#issuecomment-2903016587  

@saki7, Sometimes there is no correction possible, for example, if you want to **immediately evaluate** an arithmetic expression.  The attribute_of the RHS of expression is **NOT** an int; hence, the only way to do this is by semantic actions.  But semantic actions require modification of the context in order to store the rule's attribute and the rule's RHS attributes.  
  
Furthermore, the example provided [above,](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp#L318)   illustrates that using semantic actions is not as efficient as using the proposed "RHS" transform attribute.  
  
Furthermore, apparently I can't reopen the issue because when I clicked on the [Reopen Issue] button below, the response was "You do not have permissions to reopen this issue".

---

## Comment 3

> Username: cppljevans  
> Created at: 2025-05-23 19:19:06 UTC  
> Updated at: 2025-05-25 16:10:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/780#issuecomment-2905560439  

@saki7, in response to your request for a "minimal code", the following is such code.  The "minimal code" starts where  
the `using expression_attr=int;` occurs.  Before that is simply supporting code which actually, in the "real world", would be pulled in using #include's.  
  
```  
#define RULE_RHS_INCOMPATIBLE_MINIMAL_FILE "*/gen_transform_parser/examples/rule_rhs_incompatible-minimal.cpp"  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
namespace x3 = boost::spirit::x3;  
  
//#define USE_SEM_ACTION_FLAG  
#if !defined(USE_SEM_ACTION_FLAG)  
/*=============================================================================  
    Copyright (c) 2022 Larry Joe Evans  
    Copyright (c) 2001-2014 Joel de Guzman  
    Copyright (c) 2001-2012 Hartmut Kaiser  
    http://spirit.sourceforge.net/  
  
    Distributed under the Boost Software License, Version 1.0. (See accompanying  
    file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
#if !defined(BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_NOV_9_2022_1831CST)  
#define BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_NOV_9_2022_1831CST  
//#define BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
#ifdef BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
  #define BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_FILE "gen_transform_parser/*/traits/transform_attribute_tagged.hpp"  
  #include <boost/iostreams/utility/templ_expr/demangle_fmt_type.hpp>  
  #include <boost/utility/trace_scope.hpp>  
  #include <type_traits>  
#endif//BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
#include <boost/spirit/home/x3/core/rule_internal_id.hpp>  
#include <boost/spirit/home/x3/core/rule_external_id.hpp>  
#include <boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp>  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
    ///////////////////////////////////////////////////////////////////////////  
    //  transform_attribute_tagged  
    //  
    //  Sometimes the user needs to transform the attribute types for certain  
    //  attributes. This template can be used as a customization point, where  
    //  the user is able specify specific transformation rules for any attribute  
    //  type.  
    ///////////////////////////////////////////////////////////////////////////  
      struct   
    transform_tag_default  
      {};  
      template  
      < typename TagId  
      >  
      struct  
    transform_tag_id  
      {};  
      template  
      < typename Exposed   
        //Attribute type that's transformed from, then transformed to.  
      , typename Transformed  
        //"in between transform", i.e. used internally, i.e. not exposed.  
      , typename TransformTag=transform_tag_default  
        //distinguishes between transforms with same Exposed and Transformed  
        //but different transformations.  
        //One particulary important distinction is when TransformTag==  
        //  internal_rule_id<ID>  
        //Or:  
        //  external_rule_id<ID>  
        //where ID occurs in some rule<ID,Attribute> declaration.  
      >  
      struct   
    transform_attribute_tagged  
      ;  
      template  
      < typename Exposed  
      , typename Transformed  
      , typename RuleId  
      >  
      struct   
    transform_attribute_tagged  
      < Exposed  
      , Transformed  
      , rule_external_id<RuleId>  
      >  
      : transform_attribute<Exposed,Transformed,x3::parser_id>  
      {  
      };  
      template   
      < typename Exposed   
      , typename Transformed  
      , typename RuleId  
      >  
      struct   
    transform_attribute_tagged  
      < Exposed  
      , Transformed  
      , rule_internal_id<RuleId>  
        //This is used for "internal" transformation between  
        //a rules attribute(Exposed) and the attribute of the rule's rhs(Transformed).  
        //  
        //This default implementation does, in effect, nothing.  
      >  
      {  
        using type=Exposed&;  
        type pre(Exposed& exposed)  
          {   
        #ifdef BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
          ; trace_scope ts  
            ( stringify  
              ( BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_FILE  
              , ':'  
              , __LINE__  
              , ':'  
              , __func__  
              )  
            )  
          ; std::cout<<":Exposed=\n"<<demangle_fmt_type<Exposed>()<<";\n"  
        #endif//BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
          ; return exposed  
          ;}  
        auto post(Exposed& exposed, Exposed& transformed, bool passed)  
          {   
        #ifdef BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
          ; trace_scope ts  
            ( stringify  
              ( BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_FILE  
              , ':'  
              , __LINE__  
              , ':'  
              , __func__  
              )  
            )  
          ; std::cout<<":Exposed&=\n"<<demangle_fmt_type<Exposed>()<<";\n"  
          ; std::cout<<":Transformed&=\n"<<demangle_fmt_type<Transformed>()<<";\n"  
        #endif//BOOST_SPIRIT_X3_TRAITS_TRANSFORM_ATTRIBUTE_TAGGED_TRACE  
          ; return passed  
          ;}  
      ;};  
}}}}  
  
#endif  
/*=============================================================================  
    Copyright (c) 2022 Larry Joe Evans  
  
    Distributed under the Boost Software License, Version 1.0. (See accompanying  
    file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
==============================================================================*/  
#if !defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_JANUARY_01_2022_0853AM)  
#define BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_JANUARY_01_2022_0853AM  
//#define BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
#ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
  #define BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE "gen_transform_parser/*/core/gen_transform_parser.hpp"  
  #include <boost/spirit/home/x3/support/traits/print_attribute.hpp>  
#endif  
#if defined(USE_DEBUG) || defined(USE_RULE_DEFN)  
  #include <boost/spirit/home/x3/nonterminal/detail/rule_id.hpp>  
#endif  
//#define USE_AS_DEFN_FLAG  
#ifdef USE_AS_DEFN_FLAG  
  #include <boost/spirit/home/x3/nonterminal/rule_fwd.hpp>  
#endif//USE_AS_DEFN_FLAG  
#include <boost/spirit/home/x3/support/traits/transform_attribute_tagged.hpp>  
#include <boost/spirit/home/x3/support/traits/attribute_of.hpp>  
#include <boost/spirit/home/x3/core/parse.hpp>  
  
namespace boost{namespace spirit{namespace x3{namespace traits  
{  
  template  
  < typename Derived  
  , typename ToAttribute  
  , typename TransformTag  
  >  
struct transform_parser_attribute_crtp  
  : x3::parser  
    < transform_parser_attribute_crtp  
      < Derived  
      , ToAttribute  
      , TransformTag  
      >  
    >  
  {  
        auto&  
      derived()  
        { return static_cast<Derived&>(*this)  
        ;}  
        auto const&  
      derived()const  
        { return static_cast<Derived const&>(*this)  
        ;}  
        template  
        < typename Iterator  
        , typename Ctx  
        , typename Rctx  
        >  
        bool   
      parse  
        ( Iterator& f, Iterator l  
        , Ctx&context  
        , Rctx&r_ctx//rule context, i.e. the attribute of the calling rule.  
        , ToAttribute& to_attr  
        ) const  
        {  
        ; auto from_parser_v=static_cast<Derived const*>(this)->parser()  
        ; using from_parser_t=std::remove_const_t<decltype(from_parser_v)>  
        ; using from_attribute_type=typename attribute_of<from_parser_t,Ctx>::type  
        ;  
          using transform =   
              traits::transform_attribute_tagged  
              < ToAttribute  
              , from_attribute_type  
              , TransformTag  
              >;  
        #ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
          boost::trace_scope ts  
            ( stringify  
              ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
              , ':', __LINE__  
              , ':', __func__,"(,Ctx&,Rctx&,ToAttribute&)"  
              )  
            );  
          std::cout<<":from_parser_t=\n"<<demangle_fmt_type<from_parser_t>()<<";\n";  
          std::cout<<":ToAttribute=\n"<<demangle_fmt_type<ToAttribute>()<<";\n";  
          std::cout<<":TransformTag=\n"<<demangle_fmt_type<TransformTag>()<<";\n";  
          std::cout<<":from_attribute_type=\n"<<demangle_fmt_type<from_attribute_type>()<<";\n";            
          std::cout<<":transform_type=\n"<<demangle_fmt_type<transform_type>()<<";\n";  
          //transform::trace_tmpl();  
        #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
            transform  
          xform  
            ;  
            auto  
          from_attr  
            =xform.pre  
             ( to_attr  
           #ifdef USE_TRANSFORM_ATTRIBUTE_TAGGED_INHERITED  
             , r_ctx   
               //this allows construction of from_attr to  
               //inherit from r_ctx, which is the attribute of  
               //calling rule, if it exists.  
           #endif  
             );  
            bool   
          result=  
            derived().parse_from  
            ( f, l  
            , context  
            , r_ctx  
            , from_attr  
            );  
        #ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
          std::cout<<BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE<<':'<<__LINE__<<":result.parse="<<result<<";\n";  
        #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
          if(result)  
          {  
              result=xform.post(to_attr,from_attr,result);  
            #ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
              std::cout<<BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE<<':'<<__LINE__<<":result.post="<<result<<";\n";  
              std::cout<<":input="<<std::string(f,l)<<";\n";  
              //std::cout<<":to_attr="<<to_attr<<";\n";  
            #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
          }  
          return result;  
        }  
  };  
  template  
  < typename FromParser  
  , typename ToAttribute  
  , typename TransformTag=transform_tag_default  
  >  
struct transform_parser_attribute  
  : transform_parser_attribute_crtp  
    < transform_parser_attribute  
      < FromParser  
      , ToAttribute  
      , TransformTag  
      >  
    , ToAttribute  
    , TransformTag  
    >  
  //A parser using FromParser, but transforming FromParser's attribute   
  //to a ToAttribute.  
  {  
        using   
      attribute_type=ToAttribute  
        ;  
        FromParser   
      from_parser  
        ;  
        auto   
      parser  
        (  
        )const  
        { return from_parser  
        ;}  
        template  
        < typename Iterator  
        , typename Ctx  
        , typename Rctx  
        , typename Attribute  
        >  
        bool   
      parse_from  
        ( Iterator& f, Iterator l  
        , Ctx&context  
        , Rctx&r_ctx//rule context, i.e. the attribute of the calling rule.  
        , Attribute& attr  
        ) const  
        {  
        #if 1 && defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE)  
          boost::trace_scope ts  
          ( stringify  
            ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
            , ':', __LINE__  
            , ':', __func__  
            )  
          );  
          std::cout<<":*this="<<demangle_fmt_type<transform_parser_attribute>()<<";\n";  
          std::cout<<":Attribute=\n"<<demangle_fmt_type<Attribute>()<<";\n";  
        #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
        ; bool result=from_parser.parse(f,l,context,r_ctx,attr);  
        #if 1 && defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE)  
          std::cout<<":result="<<result<<";\n"  
        #endif  
        ; return result;  
        ;}  
      transform_parser_attribute(FromParser p)  
        : from_parser(p)  
        {  
          #if 0 && defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE)  
            boost::trace_scope ts  
            ( stringify  
              ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
              , ':', __LINE__  
              , ':', __func__,"(FromParser p)"  
              )  
            );  
            std::cout<<":*this="<<demangle_fmt_type<transform_parser_attribute>()<<";\n";  
          #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
        }  
  };  
struct ident_transform  
  {};  
  template  
  < typename FromParser  
  , typename ToAttribute  
  >  
struct transform_parser_attribute  
  < FromParser  
  , ToAttribute  
  , ident_transform  
  >   
  : x3::parser  
    < transform_parser_attribute  
      < FromParser  
      , ToAttribute  
      , ident_transform  
      >  
    >  
  {  
  private:  
      FromParser from_parser;  
  public:  
      using attribute_type = ToAttribute;  
      transform_parser_attribute(FromParser p)  
        : from_parser(p)  
        {  
          #if 0 && defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE)  
            boost::trace_scope ts  
            ( stringify  
              ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
              , ':', __LINE__  
              , ':', __func__,"(FromParser p)"  
              )  
            );  
            std::cout<<":*this="<<demangle_fmt_type<transform_parser_attribute>()<<";\n";  
          #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
        }  
        template  
        < typename Iterator  
        , typename Ctx  
        , typename Rctx  
        >  
        bool   
      parse  
        ( Iterator& f, Iterator l  
        , Ctx&context  
        , Rctx&r_ctx  
        , ToAttribute& to_attr  
        ) const  
        {   
          #ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
            boost::trace_scope ts  
              ( stringify  
                ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
                , ':', __LINE__  
                , ':', __func__,"(,Ctx&,Rctx&,ToAttribute&)"  
                )  
              );  
            std::cout<<":FromParser="<<demangle_fmt_type<FromParser>()<<";\n";  
            std::cout<<":ToAttribute="<<demangle_fmt_type<ToAttribute>()<<";\n";  
            std::cout<<":TransformTag="<<demangle_fmt_type<ident_transform>()<<";\n";  
            std::cout<<":Ctx="<<demangle_fmt_type<Ctx>()<<";\n";  
            std::cout<<":Rctx="<<demangle_fmt_type<Rctx>()<<";\n";  
          #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
        ; bool result=from_parser.parse(f,l,context,r_ctx,to_attr)  
          #ifdef BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
          ; std::cout<<BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE<<':'<<__LINE__<<":result.parse="<<result<<";\n";  
            std::cout<<":input="<<std::string(f,l)<<";\n";  
            std::cout<<":to_attr=";  
            traits::print_attribute(std::cout,to_attr);  
            std::cout<<";\n";  
          #endif//BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE  
        ; return result  
        ;}  
  };  
  template  
  < typename ToAttribute  
  , typename FromParser  
  , typename TransformTag=transform_tag_default  
  >  
  auto  
gen_transform_parser  
  ( FromParser from_parser  
  )  
  {  return transform_parser_attribute<FromParser,ToAttribute,TransformTag>{from_parser};  
  }  
#ifdef USE_AS_DEFN_FLAG    
//{as_defn  
//the following copy&pasted with only minor modifications from sehe's 'as'   
// alias template(https://en.cppreference.com/w/cpp/language/type_alias)  
// here:  
//  https://stackoverflow.com/questions/56819120/spirit-x3-how-to-get-attribute-type-to-match-rule-type?rq=1  
//=========  
      template  
      < typename ToAttribute  
      , typename TransformTag=transform_tag_default  
      >  
      struct   
    as_defn_type  
      {  
            template   
            < typename FromParser  
            >  
            constexpr auto   
          operator[](FromParser e) const   
            { return   
                x3::rule<TransformTag, ToAttribute> {} = e  
                //rule_definition<TransformTag,FromParser,ToAttribute>  
                ;  
            }  
      };  
        template  
        < typename ToAttribute  
        , typename TransformTag=transform_tag_default  
        >  
        static inline constexpr   
      as_defn_type  
        < ToAttribute  
        , TransformTag  
        >   
    as_defn  
      ;    
//}as_defn  
#endif//USE_AS_DEFN_FLAG    
      template   
      < typename ToAttribute  
      , typename TransformTag=transform_tag_default  
      >  
      struct   
    as_attr_type   
      {  
            template <typename Parser>  
            constexpr auto   
          operator[](Parser e) const   
            {   
            #if 0 && defined(BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_TRACE)  
              boost::trace_scope ts  
                ( stringify  
                  ( BOOST_SPIRIT_X3_CORE_GEN_TRANSFORM_PARSER_FILE  
                  , ':', __LINE__  
                  , ':', __func__,"(Parser e)"  
                  )  
                );  
              std::cout<<":ToAttribute=\n"<<demangle_fmt_type<ToAttribute>()<<";\n";  
              std::cout<<":Parser=\n"<<demangle_fmt_type<Parser>()<<";\n";  
            #endif  
              return   
                gen_transform_parser  
                < ToAttribute  
                , Parser  
                , TransformTag  
                >  
                ( e  
                );  
            }  
      };  
        template  
        < typename ToAttribute  
        , typename TransformTag=transform_tag_default  
        >  
        static inline constexpr   
      as_attr_type  
        < ToAttribute  
        , TransformTag  
        >   
    as_attr  
      ;  
      template   
      < typename ToAttribute  
      >  
      struct   
    as_ident_type   
      {  
            template <typename Parser>  
            constexpr auto   
          operator[](Parser e) const   
            { return   
                gen_transform_parser  
                < ToAttribute  
                , Parser  
                , ident_transform  
                >  
                ( e  
                );  
            }  
      };  
        template   
        < typename ToAttribute  
        >  
        static inline constexpr   
      as_ident_type  
        < ToAttribute  
        >   
    as_ident  
      ;    
      template  
      < typename TransformTag=ident_transform  
      >  
      struct   
    as_aof_ident_type   
      {  
            template <typename FromParser>  
            constexpr auto   
          operator[](FromParser e) const   
            { using ToAttribute=typename  
                attribute_of  
                < FromParser  
                , x3::unused_type  
                >::type  
                ;  
              return   
                gen_transform_parser  
                < ToAttribute  
                , FromParser  
                , TransformTag  
                >  
                ( e  
                );  
            }  
      };  
        static inline constexpr   
      as_aof_ident_type  
    as_aof_ident  
      ;    
//}the following copy&pasted...  
}//traits  
}//x3  
}//spirit  
}//boost  
#endif  
  
#endif//USE_SEM_ACTION_FLAG  
  
using expression_attr= int;  
  
  struct  
expression_id  
  {};  
  auto  
expression_rhs  
  =  x3::int_   
//#define GEN_COMPILE_ERROR  
#ifndef GEN_COMPILE_ERROR  
  >> '+'   
  >> x3::int_  
#endif//GEN_COMPILE_ERROR  
  ;  
  auto   
rhs_reduce=[](auto const& transformed)  
  {   
  ; auto const& attr0=boost::fusion::at_c<0>(transformed)  
  ; auto const& attr1=boost::fusion::at_c<1>(transformed)  
  ; return attr0+attr1  
  ;};  
#ifdef USE_SEM_ACTION_FLAG  
  auto   
rule_reduce=[](auto const& ctx)  
  {   
  ; auto&exposed=x3::_val(ctx)  
  ; exposed=rhs_reduce(x3::_attr(ctx))  
  ; std::cout<<RULE_RHS_INCOMPATIBLE_MINIMAL_FILE<<':'<<__LINE__<<':'<<__func__<<":exposed="<<exposed<<";\n"  
  ;};  
  auto  
expression_def=  
  expression_rhs  
  [ rule_reduce  
  ]  
  ;  
  x3::rule<expression_id, expression_attr>  
expression =   
  "expression_rule"  
  ;  
BOOST_SPIRIT_DEFINE(expression)  
  
#else  
  template  
  < typename Transformed  
  >  
  struct  
  x3::traits::  
transform_attribute_tagged  
  < expression_attr  
  , Transformed  
  , expression_id  
  >  
  {  
        using  
      Exposed=  
        expression_attr  
        ;  
        using  
      Tag=  
        expression_id  
        ;  
        Transformed  
      pre(Exposed&)  
        {  
        ; return Transformed{}  
        ;}  
        template  
        < typename Type  
        >  
        bool  
      post(Exposed& exposed, Type& transformed, bool success)  
        {   
        ; if(success)  
          { exposed=rhs_reduce(transformed)  
          ; std::cout<<RULE_RHS_INCOMPATIBLE_MINIMAL_FILE<<':'<<__LINE__<<':'<<__func__<<":exposed="<<exposed<<";\n"  
          ;}  
        ; return success    
        ;}  
  };//transform_attribute_tagged<,,expression_id>  
  auto   
expression =  
  x3::traits::as_attr  
  < expression_attr  
  , expression_id  
  >  
  [ expression_rhs  
  ]  
  ;  
#endif//USE_SEM_ACTION_FLAG  
#include <string>  
int main()  
{  
        std::string str="1+2";  
        using iterator_type=std::string::iterator;  
        iterator_type iter = str.begin();  
        iterator_type end = str.end();  
          int   
        result  
           ;  
        boost::spirit::x3::ascii::space_type space;  
        bool r =   
          phrase_parse  
          ( iter  
          , end  
          , expression  
          , space  
          , result  
          );  
  
        if (r && iter == end)  
        {  
            std::cout << "-------------------------\n";  
            std::cout << "Parsing succeeded\n";  
            std::cout << "result:\n" << result << std::endl;  
            std::cout << "-------------------------\n";  
        }  
        else  
        {  
            std::string rest(iter, end);  
            std::cout << "-------------------------\n";  
            std::cout << "Parsing failed\n";  
            std::cout << "stopped at: \"" << rest << "\"\n";  
            std::cout << "-------------------------\n";  
        }  
; return 0  
;}  
  
```  
There are 2 macros, USE_SEM_ACTION_FLAG and GEN_COMPILE_ERROR.  
When defined(USE_SEM_ACTION_FLAG):  
  
  1) a rule and   
  2) a semantic action  
  3) a BOOST_SPIRIT DEFINE  
  
are needed.  
  
OTOH, when !defined(USE_SEM_ACTION_FLAG), only:  
  
  1)  a specialization of transform_attribute_tagged  
  2) a call to x3::traits::as_attr  
  
are needed.  
  
Furthermore, you can see, when defined(GEN_COMPILE_ERROR), much  
less noise in the compiler error messages ensue when !defined(USE_SEM_ACTION_FLAG).  
  
To summarize, don't use a sledge hammer   
  (a rule with semantic actions)   
to do what a simple pin hammer can do  
  (convert a rhs attribute to a "reduced" attribute, a.k.a. transform_attribute_tagged+as_attr).  
   
-regards,  
Larry
