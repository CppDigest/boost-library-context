# #96 - Suggestion: add label for callable nodes [Open]

> Username: goldnd  
> Created at: 2019-11-07 17:21:06 UTC  
> Updated at: 2020-01-02 19:39:26 UTC  
> Url: https://github.com/boostorg/yap/issues/96  

I've been familiarizing myself with yap.  As I've been implementing new callable structs, it has been helpful to add a label to the struct that is handled by yap::print if present.  Is this of general interest or is there a better way to accomplish this task?

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-01-01 20:08:33 UTC  
> Url: https://github.com/boostorg/yap/issues/96#issuecomment-570079980  

Perhaps....  Could you provide more details?  Is the label a nested type, or something else?  What does yap::print print out when the label is present?

---

## Comment 2

> Username: goldnd  
> Created at: 2020-01-02 19:37:09 UTC  
> Url: https://github.com/boostorg/yap/issues/96#issuecomment-570318133  

I ended up not using yap so this is a little rusty but here is my modified print.hpp.  This way types with a static constexpr char* label member would have the extra label printed as a type annotation.  
```  
// Copyright (C) 2016-2018 T. Zachary Laine  
//  
// Distributed under the Boost Software License, Version 1.0. (See  
// accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
#ifndef BOOST_YAP_PRINT_HPP_INCLUDED  
#define BOOST_YAP_PRINT_HPP_INCLUDED  
  
#include <boost/yap/algorithm_fwd.hpp>  
  
#include <boost/hana/for_each.hpp>  
#include <boost/type_index.hpp>  
#include <iostream>  
  
  
namespace boost { namespace yap {  
  
    /** Returns the <code>char const *</code> string for the spelling of the  
        C++ operator associated with \a kind.  It returns the special values  
        "ref" and "term" for the non-operator kinds  
        <code>expr_kind::expr_ref</code> amd <code>expr_kind::terminal</code>,  
        respectively.*/  
    inline char const * op_string(expr_kind kind)  
    {  
        switch (kind) {  
        case expr_kind::expr_ref: return "ref";  
  
        case expr_kind::terminal: return "term";  
  
        case expr_kind::unary_plus: return "+";  
        case expr_kind::negate: return "-";  
        case expr_kind::dereference: return "*";  
        case expr_kind::complement: return "~";  
        case expr_kind::address_of: return "&";  
        case expr_kind::logical_not: return "!";  
        case expr_kind::pre_inc: return "++";  
        case expr_kind::pre_dec: return "--";  
        case expr_kind::post_inc: return "++(int)";  
        case expr_kind::post_dec: return "--(int)";  
  
        case expr_kind::shift_left: return "<<";  
        case expr_kind::shift_right: return ">>";  
        case expr_kind::multiplies: return "*";  
        case expr_kind::divides: return "/";  
        case expr_kind::modulus: return "%";  
        case expr_kind::plus: return "+";  
        case expr_kind::minus: return "-";  
        case expr_kind::less: return "<";  
        case expr_kind::greater: return ">";  
        case expr_kind::less_equal: return "<=";  
        case expr_kind::greater_equal: return ">=";  
        case expr_kind::equal_to: return "==";  
        case expr_kind::not_equal_to: return "!=";  
        case expr_kind::logical_or: return "||";  
        case expr_kind::logical_and: return "&&";  
        case expr_kind::bitwise_and: return "&";  
        case expr_kind::bitwise_or: return "|";  
        case expr_kind::bitwise_xor: return "^";  
        case expr_kind::comma: return ",";  
        case expr_kind::mem_ptr: return "->*";  
        case expr_kind::assign: return "=";  
        case expr_kind::shift_left_assign: return "<<=";  
        case expr_kind::shift_right_assign: return ">>=";  
        case expr_kind::multiplies_assign: return "*=";  
        case expr_kind::divides_assign: return "/=";  
        case expr_kind::modulus_assign: return "%=";  
        case expr_kind::plus_assign: return "+=";  
        case expr_kind::minus_assign: return "-=";  
        case expr_kind::bitwise_and_assign: return "&=";  
        case expr_kind::bitwise_or_assign: return "|=";  
        case expr_kind::bitwise_xor_assign: return "^=";  
        case expr_kind::subscript: return "[]";  
  
        case expr_kind::if_else: return "?:";  
  
        case expr_kind::call: return "()";  
  
        default: return "** ERROR: UNKNOWN OPERATOR! **";  
        }  
    }  
  
    namespace detail {  
  
        inline std::ostream & print_kind(std::ostream & os, expr_kind kind)  
        {  
            return os << op_string(kind);  
        }  
  
        template<typename T, typename = void_t<>>  
        struct printer  
        {  
            std::ostream & operator()(std::ostream & os, T const &)  
            {  
                return os << "<<unprintable-value>>";  
            }  
        };  
  
        template<typename T>  
        struct printer<  
            T,  
            void_t<decltype(  
                std::declval<std::ostream &>() << std::declval<T const &>())>>  
        {  
            std::ostream & operator()(std::ostream & os, T const & x)  
            {  
                return os << x;  
            }  
        };  
  
        template<typename T>  
        inline std::ostream & print_value(std::ostream & os, T const & x)  
        {  
            return printer<T>{}(os, x);  
        }  
  
        template<long long I>  
        inline std::ostream & print_value(std::ostream & os, hana::llong<I>)  
        {  
            return os << I << "_p";  
        }  
  
        template<typename T>  
        std::ostream & print_type(std::ostream & os, hana::tuple<T> const &)  
        {  
            os << typeindex::type_id<T>().pretty_name();  
            if (std::is_const<T>::value)  
                os << " const";  
            if (std::is_volatile<T>::value)  
                os << " volatile";  
            if (std::is_lvalue_reference<T>::value)  
                os << " &";  
            if (std::is_rvalue_reference<T>::value)  
                os << " &&";  
            return os;  
        }  
  
        template<typename T>  
        bool is_const_expr_ref(T const &)  
        {  
            return false;  
        }  
        template<typename T, template<expr_kind, class> class expr_template>  
        bool is_const_expr_ref(  
            expr_template<expr_kind::expr_ref, hana::tuple<T const *>> const &)  
        {  
            return true;  
        }  
  
  
       template <typename T, typename = int>  
       struct HasLabel : std::false_type { };  
  
       template <typename T>  
       struct HasLabel <T, decltype((void) T::label, 0)> : std::true_type { };  
  
       template <typename T >  
       constexpr bool hasLabel()  
       {  
  
               return HasLabel< T >::value;  
       }  
#ifdef BOOST_NO_CONSTEXPR_IF  
  
        template<expr_kind Kind>  
        struct print_impl  
        {  
            template<typename Expr>  
            std::ostream & operator()(  
                std::ostream & os,  
                Expr const & expr,  
                int indent,  
                char const * indent_str,  
                bool is_ref = false,  
                bool is_const_ref = false)  
            {  
                for (int i = 0; i < indent; ++i) {  
                    os << indent_str;  
                }  
  
                os << "expr<";  
		if constexpr( hasLabel<Expr>() )  
		{  
                	os << Expr::label;  
		}	  
                ::boost::yap::detail::print_kind(os, Expr::kind);  
                os << ">";  
                if (is_const_ref)  
                    os << " const &";  
                else if (is_ref)  
                    os << " &";  
                os << "\n";  
                hana::for_each(  
                    expr.elements,  
                    [&os, indent, indent_str](auto const & element) {  
                        using element_type = decltype(element);  
                        constexpr expr_kind kind =  
                            detail::remove_cv_ref_t<element_type>::kind;  
                        print_impl<kind>{}(os, element, indent + 1, indent_str);  
                    });  
  
                return os;  
            }  
        };  
  
        template<>  
        struct print_impl<expr_kind::expr_ref>  
        {  
            template<typename Expr>  
            std::ostream & operator()(  
                std::ostream & os,  
                Expr const & expr,  
                int indent,  
                char const * indent_str,  
                bool is_ref = false,  
                bool is_const_ref = false)  
            {  
                using ref_type = decltype(::boost::yap::deref(expr));  
                constexpr expr_kind ref_kind =  
                    detail::remove_cv_ref_t<ref_type>::kind;  
                print_impl<ref_kind>{}(  
                    os,  
                    ::boost::yap::deref(expr),  
                    indent,  
                    indent_str,  
                    true,  
                    ::boost::yap::detail::is_const_expr_ref(expr));  
                return os;  
            }  
        };  
  
        template<>  
        struct print_impl<expr_kind::terminal>  
        {  
            template<typename Expr>  
            std::ostream & operator()(  
                std::ostream & os,  
                Expr const & expr,  
                int indent,  
                char const * indent_str,  
                bool is_ref = false,  
                bool is_const_ref = false)  
            {  
                for (int i = 0; i < indent; ++i) {  
                    os << indent_str;  
                }  
  
                os << "term";  
		if constexpr( hasLabel<Expr>() )  
		{  
			os << " ";  
                	os << Expr::label;  
		}	  
                os << "<";  
                ::boost::yap::detail::print_type(os, expr.elements);  
                os << ">[=";  
                ::boost::yap::detail::print_value(  
                    os, ::boost::yap::value(expr));  
                os << "]";  
                if (is_const_ref)  
                    os << " const &";  
                else if (is_ref)  
                    os << " &";  
                os << "\n";  
  
                return os;  
            }  
        };  
  
#else  
  
        template<typename Expr>  
        std::ostream & print_impl(  
            std::ostream & os,  
            Expr const & expr,  
            int indent,  
            char const * indent_str,  
            bool is_ref = false,  
            bool is_const_ref = false)  
        {  
            if constexpr (Expr::kind == expr_kind::expr_ref) {  
                print_impl(  
                    os,  
                    ::boost::yap::deref(expr),  
                    indent,  
                    indent_str,  
                    true,  
                    ::boost::yap::detail::is_const_expr_ref(expr));  
            } else {  
                for (int i = 0; i < indent; ++i) {  
                    os << indent_str;  
                }  
  
                if constexpr (Expr::kind == expr_kind::terminal) {  
                    os << "term";  
		if constexpr( hasLabel<Expr>() )  
		{  
			os << " "  
                	os << Expr::label;  
		}	  
                    os << "<";  
                    ::boost::yap::detail::print_type(os, expr.elements);  
                    os << ">[=";  
                    ::boost::yap::detail::print_value(  
                        os, ::boost::yap::value(expr));  
                    os << "]";  
                    if (is_const_ref)  
                        os << " const &";  
                    else if (is_ref)  
                        os << " &";  
                    os << "\n";  
                } else {  
                    os << "expr<";  
		if constexpr( hasLabel<Expr>() )  
		{  
                	os << Expr::label;  
		}	  
                    ::boost::yap::detail::print_kind(os, Expr::kind);  
                    os << ">";  
                    if (is_const_ref)  
                        os << " const &";  
                    else if (is_ref)  
                        os << " &";  
                    os << "\n";  
                    hana::for_each(  
                        expr.elements,  
                        [&os, indent, indent_str](auto const & element) {  
                            ::boost::yap::detail::print_impl(  
                                os, element, indent + 1, indent_str);  
                        });  
                }  
            }  
  
            return os;  
        }  
  
#endif // BOOST_NO_CONSTEXPR_IF  
    }  
  
    /** Prints expression \a expr to stream \a os.  Returns \a os. */  
    template<typename Expr>  
    std::ostream & print(std::ostream & os, Expr const & expr)  
    {  
#ifdef BOOST_NO_CONSTEXPR_IF  
        return detail::print_impl<detail::remove_cv_ref_t<Expr>::kind>{}(  
            os, expr, 0, "    ");  
#else  
        return detail::print_impl(os, expr, 0, "    ");  
#endif  
    }  
  
}}  
  
#endif  
```

---

## Comment 3

> Username: goldnd  
> Created at: 2020-01-02 19:39:26 UTC  
> Url: https://github.com/boostorg/yap/issues/96#issuecomment-570318819  

Here's an example:  
  
```  
template<yap::expr_kind Kind, typename Tuple>  
struct DomainExpr  
{  
	static yap::expr_kind const kind = Kind;  
	Tuple elements;  
	static constexpr const char* label = "Domain";  
};  
```
