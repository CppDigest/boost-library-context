# #723 - x3: annotate_on_success doesn't annotate variant well. [Closed]

> Username: goblin-rulez  
> Created at: 2022-04-24 13:23:07 UTC  
> Updated at: 2022-04-25 00:49:22 UTC  
> Closed at: 2022-04-25 00:49:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/723  

annotate_on_success(x3/support/utility/annotate_on_success.hpp) doesn't annotate when the type of ast is either  
- derived from a variant. ( not a variant itself)  
   such as  
`    struct operand : x3::variant<...>;`  
or  
- defined as forward_ast in a variant.  
  
here is the sample that reproduce the issue.  
This is basically simplified version of calc example.  
I added pos_printer that tries to print annotated information for parsed ast elements.  
```  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <boost/spirit/home/x3/support/utility/annotate_on_success.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
#include <iostream>  
#include <list>  
#include <sstream>  
  
namespace client { namespace ast  
{  
    namespace x3 = boost::spirit::x3;  
  
    struct nil{};  
    struct unary;  
    struct expression;  
  
    struct variable : x3::position_tagged  
    {  
        variable(std::string const& name = "") : name{ name} {}  
        std::string name;  
    };  
  
    struct operand : x3::variant<  
                        nil,  
                        variable,  
                        x3::forward_ast<unary>,  
                        x3::forward_ast<expression>  
                    >  
    {  
        using base_type::base_type;  
        using base_type::operator=;  
    };  
  
    struct unary : x3::position_tagged  
    {  
        char operator_;  
        operand operand_;  
    };  
  
    struct operation : x3::position_tagged  
    {  
        char operator_;  
        operand operand_;  
    };  
  
    struct expression : x3::position_tagged  
    {  
        operand first;  
        std::list<operation> rest;  
    };  
  
}}  
  
BOOST_FUSION_ADAPT_STRUCT(client::ast::unary,  
    operator_, operand_  
)  
BOOST_FUSION_ADAPT_STRUCT(client::ast::operation,  
    operator_, operand_  
)  
BOOST_FUSION_ADAPT_STRUCT(client::ast::expression,  
    first, rest  
)  
  
namespace client  
{  
    namespace parser  
    {  
        namespace x3 = boost::spirit::x3;  
        namespace ascii = boost::spirit::x3::ascii;  
  
        using x3::raw;  
        using x3::lexeme;  
        using x3::alpha;  
        using x3::alnum;  
        using ascii::char_;  
  
        struct expression_class;  
        struct multiplicative_expr_class;  
        struct unary_expr_class;  
        struct primary_expr_class;  
        struct identifier_class;  
  
        x3::rule<expression_class, ast::expression> const expression = "expression";  
        x3::rule<multiplicative_expr_class, ast::expression> const multiplicative_expr = "multiplicative_expr";  
        x3::rule<unary_expr_class, ast::operand> const unary_expr = "unary_expr";  
        x3::rule<primary_expr_class, ast::operand> const primary_expr = "primary_expr";  
        x3::rule<identifier_class, std::string> const identifier = "identifier";  
  
        auto const expression_def = multiplicative_expr;  
  
        auto const multiplicative_expr_def =  
            unary_expr  
            >>  *(  (char_('*') > unary_expr)  
                 |  (char_('/') > unary_expr)  
                 )  
            ;  
  
        auto const unary_expr_def =  
                primary_expr  
            |   (char_('-') > primary_expr)  
            |   (char_('+') > primary_expr)  
            ;  
  
        auto const primary_expr_def =  
                identifier  
            |   '(' > expression > ')'  
            ;  
  
        auto const identifier_def =  
                raw[lexeme[(alpha | '_') >> *(alnum | '_')]];  
            ;  
  
        BOOST_SPIRIT_DEFINE(  
                expression,  
                multiplicative_expr,  
                unary_expr,  
                primary_expr,  
                identifier);  
  
        struct unary_expr_class : x3::annotate_on_success {};  
        struct primary_expr_class : x3::annotate_on_success {};  
        struct identifier_class : x3::annotate_on_success {};  
    }  
  
    template<typename Error_handler>  
    struct pos_printer{  
        pos_printer(Error_handler const& handler) : error_handler{handler} {}  
  
        using result_type = void;  
        void operator()(ast::nil const&) const {}  
        void operator()(ast::unary const& x) const {  
            std::ostringstream oss;  
            oss << R"(unary ")" << x.operator_ << R"(" found. pos_ids{)" << x.id_first << ", " << x.id_last << "}\n";  
            if(x.id_first >= 0){  
                error_handler(x, oss.str());  
            }else{  
                std::cout << oss.str();  
            }  
        }  
        void operator()(ast::variable const& x) const {  
            std::ostringstream oss;  
            oss << R"(variable ")" << x.name << R"(" found. pos_ids{)" << x.id_first << ", " << x.id_last << "}\n";  
            if(x.id_first >= 0){  
                error_handler(x, oss.str());  
            }else{  
                std::cout << oss.str();  
            }  
        }  
        void operator()(ast::operation const& x) const {  
            std::ostringstream oss;  
            oss << R"(operation ")" << x.operator_ << R"(" found. pos_ids{)" << x.id_first << ", " << x.id_last << "}\n";  
            if(x.id_first >= 0){  
                error_handler(x, oss.str());  
            }else{  
                std::cout << oss.str();  
            }  
            boost::apply_visitor(*this, x.operand_);  
        }  
        void operator()(ast::expression const& x) const {  
            std::ostringstream oss;  
            oss << "expression found. pos_ids{" << x.id_first << ", " << x.id_last << "}\n";  
            if(x.id_first >= 0){  
                error_handler(x, oss.str());  
            }else{  
                std::cout << oss.str();  
            }  
            boost::apply_visitor(*this, x.first);  
            for(auto const& operation : x.rest){  
                (*this)(operation);  
            }  
        }  
  
        Error_handler const& error_handler;  
    };  
    template<typename Error_handler>  
    pos_printer(Error_handler const&) -> pos_printer<Error_handler>;  
}  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
  
///////////////////////////////////////////////////////////////////////////////  
// Our main parse entry point  
///////////////////////////////////////////////////////////////////////////////  
  
void parse(std::string const& input)  
{  
    using boost::spirit::x3::ascii::space;  
    typedef std::string::const_iterator iterator_type;  
  
    client::ast::expression ast;  
    iterator_type iter = input.begin();  
    iterator_type const end = input.end();  
  
    using boost::spirit::x3::with;  
    using boost::spirit::x3::error_handler_tag;  
    using error_handler_type = boost::spirit::x3::error_handler<iterator_type>;  
  
    // Our error handler  
    error_handler_type error_handler(iter, end, std::cerr);  
  
    // Our parser  
    using client::parser::expression;  
    auto const parser =  
        // we pass our error handler to the parser so we can access  
        // it later in our on_error and on_sucess handlers  
        with<error_handler_tag>(std::ref(error_handler))  
        [  
            expression  
        ];  
  
    bool r = phrase_parse(iter, end, parser, space, ast);  
  
    if (r && iter == end)  
    {  
        std::cout << "-------------------------\n";  
        std::cout << "Parsing succeeded\n";  
  
        auto printer = client::pos_printer(error_handler);  
        printer(ast);  
        std::cout << "\n-------------------------\n";  
  
    }  
    else  
    {  
        std::cout << "-------------------------\n";  
        std::cout << "Parsing failed\n";  
        std::cout << "-------------------------\n";  
    }  
}  
  
int main()  
{  
    parse(std::string("(a * (-b))"));  
  
    return 0;  
}  
```  
And this is the output. No position is annotated.  
```  
-------------------------  
Parsing succeeded  
expression found. pos_ids{-1, -1}  
expression found. pos_ids{-1, -1}  
variable "a" found. pos_ids{-1, -1}  
operation "*" found. pos_ids{-1, -1}  
expression found. pos_ids{-1, -1}  
unary "-" found. pos_ids{-1, -1}  
-------------------------  
```

---

## Comment 1

> Username: goblin-rulez  
> Created at: 2022-04-24 13:27:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/723#issuecomment-1107842095  

I found two reasons why annotate fails.  
  
`x3/support/utility/annotate_on_success.hpp`  
```  
    struct annotate_on_success  
    {  
        template <typename Iterator, typename Context, typename... Types>  
        inline void on_success(Iterator const& first, Iterator const& last  
          , variant<Types...>& ast, Context const& context)  
        {  
            ast.apply_visitor(x3::make_lambda_visitor<void>([&](auto& node)  
            {  
                this->on_success(first, last, node, context);  
            }));  
        }  
  
        template <typename T, typename Iterator, typename Context>  
        inline void on_success(Iterator const& first, Iterator const& last  
          , T& ast, Context const& context)  
        {  
            auto& error_handler = x3::get<error_handler_tag>(context).get();  
            error_handler.tag(ast, first, last);  
        }  
    };  
  
```  
1. The class derived from variant matches better to the generic version of on_success rather than the one specialized for variant, so apply_visitor is not executed.  
  
  
`x3/support/ast/position_tagged.hpp`  
  
```  
        // This will catch all nodes except those inheriting from position_tagged  
        template <typename AST>  
        void annotate(AST& /* ast */, iterator_type /* first */, iterator_type /* last */, mpl::false_)  
        {  
            // (no-op) no need for tags  
        }  
  
        // This will catch all nodes inheriting from position_tagged  
        void annotate(position_tagged& ast, iterator_type first, iterator_type last, mpl::true_)  
        {  
            ast.id_first = int(positions.size());  
            positions.push_back(first);  
            ast.id_last = int(positions.size());  
            positions.push_back(last);  
        }  
  
        template <typename AST>  
        void annotate(AST& ast, iterator_type first, iterator_type last)  
        {  
            annotate(ast, first, last, is_base_of<position_tagged, AST>());  
        }  
```  
2. forward_ast\<T> is not derived from position_tagged, so position_cache ignores it even if T is derived from position_tagged.  
  
  
I modified the annotate_on_success as follows, and it seems the issue is fixed.  
- disable the generic version of on_success if traits::is_variant\<T> is true  
- specialize on_success for forward_ast\<T>, and call on_success() with the result of ast.get()  
  
```  
#include <boost/spirit/home/x3/support/traits/is_variant.hpp>  
  
    struct annotate_on_success  
    {  
        template <typename Iterator, typename Context, typename... Types>  
        inline void on_success(Iterator const& first, Iterator const& last  
          , variant<Types...>& ast, Context const& context)  
        {  
            ast.apply_visitor(x3::make_lambda_visitor<void>([&](auto& node)  
            {  
                this->on_success(first, last, node, context);  
            }));  
        }  
  
        template <typename T, typename Iterator, typename Context>  
        inline void on_success(Iterator const& first, Iterator const& last  
          , forward_ast<T>& ast, Context const& context)  
        {  
            this->on_success(first, last, ast.get(), context);  
        }  
  
        template <typename T, typename Iterator, typename Context>  
        inline typename disable_if<traits::is_variant<T>>::type on_success(Iterator const& first, Iterator const& last  
          , T& ast, Context const& context)  
        {  
            auto& error_handler = x3::get<error_handler_tag>(context).get();  
            error_handler.tag(ast, first, last);  
        }  
    };  
```  
  
Now, the sample output looks like this.  
```  
-------------------------  
Parsing succeeded  
expression found. pos_ids{-1, -1}  
In line 1:  
expression found. pos_ids{14, 15}  
  
(a * (-b))  
~~~~~~~~~~ <<-- Here  
In line 1:  
variable "a" found. pos_ids{2, 3}  
  
(a * (-b))  
 ~ <<-- Here  
operation "*" found. pos_ids{-1, -1}  
In line 1:  
expression found. pos_ids{10, 11}  
  
(a * (-b))  
     ~~~~ <<-- Here  
In line 1:  
unary "-" found. pos_ids{6, 7}  
  
(a * (-b))  
      ~~ <<-- Here  
  
-------------------------  
```
