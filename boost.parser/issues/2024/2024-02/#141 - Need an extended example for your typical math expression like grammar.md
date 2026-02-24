# #141 - Need an extended example for your typical math expression like grammar [Open]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 18:50:11 UTC  
> Updated at: 2025-06-14 22:48:18 UTC  
> Url: https://github.com/boostorg/parser/issues/141  

Lots of times when people get down into the nitty gritty of using a parsing framework, it's because their ad-hoc attempts at using a regex or whatever for a complicated grammar becomes unwieldy.  One of the most common needs for using a parsing framework is when you need to parse arithmetic expressions with operator precedence similar to C/C++.  
  
An elaborated example that demonstrated parsing expression using all the typical operators:  
  
- additive operators `+`, `-` (unary and binary)  
- multiplicative operators `/`, `*`  
- bitwise operators `&`, `|`, `^`, `~`, `<<`, `>>`  
- comparison operators `==`, `!=`, `<`, `<=`, `>`, `>=`

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 23:54:56 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-1970119516  

I really don't like these kinds of examples; I've never needed to implement a calculator.  But, I suppose it would be useful to do part of this, to show how precedence can be encoded into your parsers.  I may do this, but no prommises.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2024-03-06 18:00:00 UTC  
> Updated at: 2025-06-06 14:51:43 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-1981481306  

Something that shows how to implement operator precedence would be good enough.  I have to parse a DSL that contains arbitrary math on complex numbers, so...

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-03-07 01:57:54 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-1982199321  

Gotcha, that makes sense.  I'll try to put something together pretty soon.

---

## Comment 4

> Username: d5ch4k  
> Created at: 2024-04-30 15:10:30 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2085599885  

I ported some weeks ago as an exercise the spirit::x3 example calc2.cpp augmented with building the AST and it's evaluation. It's still using integers as input (though returning double) but it's good enough for operator precedence on pocket calculator operations, i.e. `+,-,*,/, (...) `  
Probably this might help as a starter  
  
```  
/*=============================================================================  
Copyright (c) 2001-2014 Joel de Guzman  
  
Distributed under the Boost Software License, Version 1.0. (See accompanying  
file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
///////////////////////////////////////////////////////////////////////////////  
//  
//  A Calculator example demonstrating the grammar and semantic actions  
//  using lambda functions. The parser prints code suitable for a stack  
//  based virtual machine, the semantic actions create the AST and  
//  the evaluation is performed by a recursive visitor. The 'recursive' variant  
//  decoupling is done with the help of std::optional<>  
//  
//  [ JDG May 10, 2002 ]        spirit 1  
//  [ JDG March 4, 2007 ]       spirit 2  
//  [ JDG February 21, 2011 ]   spirit 2.5  
//  [ JDG June 6, 2014 ]        spirit x3 (from qi calc2, but using lambda functions)  
//  [ MPo Oct 27, 2022 ]        boost::parser (from spirit x3)  
//  [ MPo Apr 20, 2024 ]        AST, visitor  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <algorithm>  
#include <string>  
#include <deque>  
#include <limits>  
  
namespace bp = boost::parser;  
  
template<typename TVisitor, typename TVariant>  
decltype(auto) visit_node_recursively(TVisitor && visitor, TVariant && variant)  
{  
    return std::visit(  
        std::forward<TVisitor>(visitor), std::forward<TVariant>(variant));  
}  
  
namespace impl {  
    enum class ub_operator { uminus, add, subtract, multiply, divide };  
  
    struct node;  
    using onode = std::optional<node>;  
    using vnode = std::variant<unsigned int, int, float, double, onode>;  
    using node_array = std::vector<vnode>;  
    struct node  
    {  
        ub_operator op;  
        node_array nodes;  
  
        node(const ub_operator op, vnode const & arg1) : op(op), nodes{arg1} {}  
        node(const ub_operator op, vnode const & arg1, vnode const & arg2) :  
            op(op), nodes{arg1, arg2}  
        {}  
    };  
}  
  
using impl::ub_operator;  
using impl::vnode;  
using impl::onode;  
  
struct node_visitor  
{  
    double operator()(unsigned int x) const { return (double)x; }  
    double operator()(int x) const { return (double)x; }  
    double operator()(float x) const { return (double)x; }  
    double operator()(double x) const { return x; }  
  
    double operator()(onode const & pn)  
    {  
        auto & node = *pn;  
        switch (node.op) {  
        case ub_operator::uminus:  
            assert(node.nodes.size() == 1);  
            return -visit_node_recursively(*this, node.nodes[0]);  
        case ub_operator::add:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) +  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::subtract:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) -  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::multiply:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) *  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::divide:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) /  
                   visit_node_recursively(*this, node.nodes[1]);  
        default:  
            return std::numeric_limits<double>::quiet_NaN();  
        }  
    }  
};  
  
namespace client {  
    ///////////////////////////////////////////////////////////////////////////////  
    //  Semantic actions  
    ////////////////////////////////////////////////////////1///////////////////////  
    namespace {  
  
    std::deque<vnode> vn_stack;  
  
    auto do_int = [](auto & ctx) {  
        std::cout << "push " << bp::_attr(ctx) << std::endl;  
        vnode i = _attr(ctx);  
        vn_stack.push_front(i); // ast  
    };  
    auto const do_add = [](auto & ctx) {  
        std::cout << "add" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a + b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::add, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_subt = [](auto & ctx) {  
        std::cout << "subtract" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a - b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::subtract, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_mult = [](auto & ctx) {  
        std::cout << "mult" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a * b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::multiply, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_div = [](auto & ctx) {  
        std::cout << "divide" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a / b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::divide, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_neg = [](auto & ctx) {  
        std::cout << "negate" << std::endl;  
        // stack effect notation ( a -- -a )  
        {   // ast  
            assert(vn_stack.size() > 0);  
            auto && arg = vn_stack[0];  
            auto result = std::optional(impl::node(ub_operator::uminus, arg));  
            vn_stack[0] = result;  
        }  
    };  
}  
///////////////////////////////////////////////////////////////////////////////  
//  The calculator grammar  
///////////////////////////////////////////////////////////////////////////////  
namespace calculator_grammar {  
    bp::rule<class expression> const expression("expression");  
    bp::rule<class term> const term("term");  
    bp::rule<class factor> const factor("factor");  
  
  
    auto const expression_def = term >> *(('+' >> term[do_add]) |  
                            ('-' >> term[do_subt]));  
  
    auto const term_def = factor >> *(('*' >> factor[do_mult]) |  
                            ('/' >> factor[do_div]));  
  
    auto const factor_def = bp::uint_[do_int] | '(' >> expression >> ')' |  
                            ('-' >> factor[do_neg]) | ('+' >> factor);  
  
    BOOST_PARSER_DEFINE_RULES(expression, term, factor);  
  
    auto calculator = expression;  
}  
  
using calculator_grammar::calculator;  
  
}  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
int main()  
{  
    //test_recursive_node_visitor();  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Expression parser..." << std::endl << std::endl;  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Type an expression...or [q or Q] to quit"  
    << std::endl << std::endl;  
  
    typedef std::string::const_iterator iterator_type;  
  
    std::string str;  
    while (std::getline(std::cin, str)) {  
        if (str.empty() || str[0] == 'q' || str[0] == 'Q')  
            break;  
  
        auto & calc = client::calculator; // Our grammar  
  
        auto r = bp::parse(str, calc, bp::ws);  
  
        if (r) {  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing succeeded" << std::endl;  
            assert(client::vn_stack.size() == 1);  
            std::cout << str << " ==> "  
            << std::visit(node_visitor(), client::vn_stack[0])  
            << " (AST eval)"  
            << std::endl;  
            client::vn_stack.pop_front();  
            std::cout << "-------------------------" << std::endl;  
        } else {  
            iterator_type iter = str.begin();  
            iterator_type end = str.end();  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing failed" << std::endl;  
            bp::prefix_parse(iter, end, calc, bp::ws);  
            std::string rest(iter, end);  
            std::cout << "stopped at: \"" << rest << "\"" << std::endl;  
            client::vn_stack.clear();  
            std::cout << "-------------------------" << std::endl;  
        }  
        std::cout << "Type an expression...or [q or Q] to quit"  
        << std::endl << std::endl;  
    }  
  
    std::cout << "Bye... :-)" << std::endl << std::endl;  
    return 0;  
}  
```  
  
Martin

---

## Comment 5

> Username: cppljevans  
> Created at: 2024-06-10 02:24:16 UTC  
> Updated at: 2024-06-10 02:25:30 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2157065299  

> I ported some weeks ago as an exercise the spirit::x3 example calc2.cpp augmented with building the AST and it's evaluation. It's still using integers as input (though returning double) but it's good enough for operator precedence on pocket calculator operations, i.e. `+,-,*,/, (...) ` Probably this might help as a starter  
>   
  
The grammar can be simplified when one realizes that the only recursion is on the `expression`:  
```c++  
/*=============================================================================  
Copyright (c) 2001-2014 Joel de Guzman  
  
Distributed under the Boost Software License, Version 1.0. (See accompanying  
file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
///////////////////////////////////////////////////////////////////////////////  
//  
//  A Calculator example demonstrating the grammar and semantic actions  
//  using lambda functions. The parser prints code suitable for a stack  
//  based virtual machine, the semantic actions create the AST and  
//  the evaluation is performed by a recursive visitor. The 'recursive' variant  
//  decoupling is done with the help of std::optional<>  
//  
//  [ JDG May 10, 2002 ]        spirit 1  
//  [ JDG March 4, 2007 ]       spirit 2  
//  [ JDG February 21, 2011 ]   spirit 2.5  
//  [ JDG June 6, 2014 ]        spirit x3 (from qi calc2, but using lambda functions)  
//  [ MPo Oct 27, 2022 ]        boost::parser (from spirit x3)  
//  [ MPo Apr 20, 2024 ]        AST, visitor  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <algorithm>  
#include <string>  
#include <deque>  
#include <limits>  
  
namespace bp = boost::parser;  
  
template<typename TVisitor, typename TVariant>  
decltype(auto) visit_node_recursively(TVisitor && visitor, TVariant && variant)  
{  
    return std::visit(  
        std::forward<TVisitor>(visitor), std::forward<TVariant>(variant));  
}  
  
namespace impl {  
    enum class ub_operator { uminus, add, subtract, multiply, divide };  
  
    struct node;  
    using onode = std::optional<node>;  
    using vnode = std::variant<unsigned int, int, float, double, onode>;  
    using node_array = std::vector<vnode>;  
    struct node  
    {  
        ub_operator op;  
        node_array nodes;  
  
        node(const ub_operator op, vnode const & arg1) : op(op), nodes{arg1} {}  
        node(const ub_operator op, vnode const & arg1, vnode const & arg2) :  
            op(op), nodes{arg1, arg2}  
        {}  
    };  
}  
  
using impl::ub_operator;  
using impl::vnode;  
using impl::onode;  
  
struct node_visitor  
{  
    double operator()(unsigned int x) const { return (double)x; }  
    double operator()(int x) const { return (double)x; }  
    double operator()(float x) const { return (double)x; }  
    double operator()(double x) const { return x; }  
  
    double operator()(onode const & pn)  
    {  
        auto & node = *pn;  
        switch (node.op) {  
        case ub_operator::uminus:  
            assert(node.nodes.size() == 1);  
            return -visit_node_recursively(*this, node.nodes[0]);  
        case ub_operator::add:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) +  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::subtract:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) -  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::multiply:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) *  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::divide:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) /  
                   visit_node_recursively(*this, node.nodes[1]);  
        default:  
            return std::numeric_limits<double>::quiet_NaN();  
        }  
    }  
};  
  
namespace client {  
    ///////////////////////////////////////////////////////////////////////////////  
    //  Semantic actions  
    ////////////////////////////////////////////////////////1///////////////////////  
    namespace {  
  
    std::deque<vnode> vn_stack;  
  
    auto do_int = [](auto & ctx) {  
        std::cout << "push " << bp::_attr(ctx) << std::endl;  
        vnode i = _attr(ctx);  
        vn_stack.push_front(i); // ast  
    };  
    auto const do_add = [](auto & ctx) {  
        std::cout << "add" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a + b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::add, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_subt = [](auto & ctx) {  
        std::cout << "subtract" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a - b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::subtract, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_mult = [](auto & ctx) {  
        std::cout << "mult" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a * b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::multiply, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_div = [](auto & ctx) {  
        std::cout << "divide" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a / b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::divide, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_neg = [](auto & ctx) {  
        std::cout << "negate" << std::endl;  
        // stack effect notation ( a -- -a )  
        {   // ast  
            assert(vn_stack.size() > 0);  
            auto && arg = vn_stack[0];  
            auto result = std::optional(impl::node(ub_operator::uminus, arg));  
            vn_stack[0] = result;  
        }  
    };  
}  
///////////////////////////////////////////////////////////////////////////////  
//  The calculator grammar  
///////////////////////////////////////////////////////////////////////////////  
namespace calculator_grammar {  
    bp::rule<class expression> const expression("expression");  
  
    auto const uint = bp::uint_[do_int];  
  
    auto const factor = uint | '(' >> expression >> ')' |  
                            ('-' >> uint[do_neg]) | ('+' >> uint);  
  
    auto const term = factor >> *(('*' >> factor[do_mult]) |  
                            ('/' >> factor[do_div]));  
  
    auto const expression_def = term >> *(('+' >> term[do_add]) |  
                            ('-' >> term[do_subt]));  
  
    BOOST_PARSER_DEFINE_RULES(expression);  
  
    auto calculator = expression;  
}  
  
using calculator_grammar::calculator;  
  
}  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
int main()  
{  
    //test_recursive_node_visitor();  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Expression parser..." << std::endl << std::endl;  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
  
    std::string inputs[]=  
      { "999"  
      , "-888"  
      , "1+2"  
      , "2*3"  
      , "2+(3*4)"  
      };  
    for(std::string str:inputs) {  
  
        auto & calc = client::calculator; // Our grammar  
  
        auto r = bp::parse(str, calc, bp::ws);  
  
        if (r) {  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing succeeded" << std::endl;  
            assert(client::vn_stack.size() == 1);  
            std::cout << str << " ==> "  
            << std::visit(node_visitor(), client::vn_stack[0])  
            << " (AST eval)"  
            << std::endl;  
            client::vn_stack.pop_front();  
            std::cout << "-------------------------" << std::endl;  
        } else {  
            typedef std::string::const_iterator iterator_type;  
            iterator_type iter = str.begin();  
            iterator_type end = str.end();  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing failed" << std::endl;  
            bp::prefix_parse(iter, end, calc, bp::ws);  
            std::string rest(iter, end);  
            std::cout << "stopped at: \"" << rest << "\"" << std::endl;  
            client::vn_stack.clear();  
            std::cout << "-------------------------" << std::endl;  
        }  
    }  
  
    std::cout << "Bye... :-)" << std::endl << std::endl;  
    return 0;  
}  
```  
which produces the expected output:  
```  
/////////////////////////////////////////////////////////  
  
Expression parser...  
  
/////////////////////////////////////////////////////////  
  
push 999  
-------------------------  
Parsing succeeded  
999 ==> 999 (AST eval)  
-------------------------  
push 888  
negate  
-------------------------  
Parsing succeeded  
-888 ==> -888 (AST eval)  
-------------------------  
push 1  
push 2  
add  
-------------------------  
Parsing succeeded  
1+2 ==> 3 (AST eval)  
-------------------------  
push 2  
push 3  
mult  
-------------------------  
Parsing succeeded  
2*3 ==> 6 (AST eval)  
-------------------------  
push 2  
push 3  
push 4  
mult  
add  
-------------------------  
Parsing succeeded  
2+(3*4) ==> 14 (AST eval)  
-------------------------  
Bye... :-)  
```

---

## Comment 6

> Username: LegalizeAdulthood  
> Created at: 2024-06-10 16:31:12 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2158826629  

Thank you @cppljevans and @d5ch4k these examples are very helpful.

---

## Comment 7

> Username: LegalizeAdulthood  
> Created at: 2024-06-10 16:38:29 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2158838951  

>       , "2+(3*4)"  
  
@cppljevans Why did you put parentheses around `3*4`?  Operator precedence (multiplication binds tighter than addition) should have been enough.

---

## Comment 8

> Username: cppljevans  
> Created at: 2024-06-10 17:31:32 UTC  
> Updated at: 2024-06-10 18:30:53 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2158928953  

> > ```  
> >   , "2+(3*4)"  
> > ```  
>   
> @cppljevans Why did you put parentheses around `3*4`? Operator precedence (multiplication binds tighter than addition) should have been enough.  
  
Very simple reason.  It was an oversight.  Thanks for catching it.  
  
Here's corrected code (with slight change in uint parser):  
```c++  
/*=============================================================================  
Copyright (c) 2001-2014 Joel de Guzman  
  
Distributed under the Boost Software License, Version 1.0. (See accompanying  
file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
///////////////////////////////////////////////////////////////////////////////  
//  
//  A Calculator example demonstrating the grammar and semantic actions  
//  using lambda functions. The parser prints code suitable for a stack  
//  based virtual machine, the semantic actions create the AST and  
//  the evaluation is performed by a recursive visitor. The 'recursive' variant  
//  decoupling is done with the help of std::optional<>  
//  
//  [ JDG May 10, 2002 ]        spirit 1  
//  [ JDG March 4, 2007 ]       spirit 2  
//  [ JDG February 21, 2011 ]   spirit 2.5  
//  [ JDG June 6, 2014 ]        spirit x3 (from qi calc2, but using lambda functions)  
//  [ MPo Oct 27, 2022 ]        boost::parser (from spirit x3)  
//  [ MPo Apr 20, 2024 ]        AST, visitor  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <algorithm>  
#include <string>  
#include <deque>  
#include <limits>  
  
namespace bp = boost::parser;  
  
template<typename TVisitor, typename TVariant>  
decltype(auto) visit_node_recursively(TVisitor && visitor, TVariant && variant)  
{  
    return std::visit(  
        std::forward<TVisitor>(visitor), std::forward<TVariant>(variant));  
}  
  
namespace impl {  
    enum class ub_operator { uminus, add, subtract, multiply, divide };  
  
    struct node;  
    using onode = std::optional<node>;  
    using vnode = std::variant<unsigned int, int, float, double, onode>;  
    using node_array = std::vector<vnode>;  
    struct node  
    {  
        ub_operator op;  
        node_array nodes;  
  
        node(const ub_operator op, vnode const & arg1) : op(op), nodes{arg1} {}  
        node(const ub_operator op, vnode const & arg1, vnode const & arg2) :  
            op(op), nodes{arg1, arg2}  
        {}  
    };  
}  
  
using impl::ub_operator;  
using impl::vnode;  
using impl::onode;  
  
struct node_visitor  
{  
    double operator()(unsigned int x) const { return (double)x; }  
    double operator()(int x) const { return (double)x; }  
    double operator()(float x) const { return (double)x; }  
    double operator()(double x) const { return x; }  
  
    double operator()(onode const & pn)  
    {  
        auto & node = *pn;  
        switch (node.op) {  
        case ub_operator::uminus:  
            assert(node.nodes.size() == 1);  
            return -visit_node_recursively(*this, node.nodes[0]);  
        case ub_operator::add:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) +  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::subtract:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) -  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::multiply:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) *  
                   visit_node_recursively(*this, node.nodes[1]);  
        case ub_operator::divide:  
            assert(node.nodes.size() == 2);  
            return visit_node_recursively(*this, node.nodes[0]) /  
                   visit_node_recursively(*this, node.nodes[1]);  
        default:  
            return std::numeric_limits<double>::quiet_NaN();  
        }  
    }  
};  
  
namespace client {  
    ///////////////////////////////////////////////////////////////////////////////  
    //  Semantic actions  
    ////////////////////////////////////////////////////////1///////////////////////  
    namespace {  
  
    std::deque<vnode> vn_stack;  
  
    auto do_int = [](auto & ctx) {  
        std::cout << "push " << bp::_attr(ctx) << std::endl;  
        vnode i = _attr(ctx);  
        vn_stack.push_front(i); // ast  
    };  
    auto const do_add = [](auto & ctx) {  
        std::cout << "add" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a + b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::add, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_subt = [](auto & ctx) {  
        std::cout << "subtract" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a - b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::subtract, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_mult = [](auto & ctx) {  
        std::cout << "mult" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a * b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::multiply, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_div = [](auto & ctx) {  
        std::cout << "divide" << std::endl;  
        // stack effect notation ( a, b -- c ) where c = a / b  
        {   // ast  
            assert(vn_stack.size() > 1);  
            auto && second = vn_stack[0];  
            auto && first = vn_stack[1];  
            auto result = std::optional(impl::node(ub_operator::divide, first, second));  
            vn_stack[1] = result;  
            vn_stack.pop_front();  
        }  
    };  
    auto const do_neg = [](auto & ctx) {  
        std::cout << "negate" << std::endl;  
        // stack effect notation ( a -- -a )  
        {   // ast  
            assert(vn_stack.size() > 0);  
            auto && arg = vn_stack[0];  
            auto result = std::optional(impl::node(ub_operator::uminus, arg));  
            vn_stack[0] = result;  
        }  
    };  
}  
///////////////////////////////////////////////////////////////////////////////  
//  The calculator grammar  
///////////////////////////////////////////////////////////////////////////////  
namespace calculator_grammar {  
    bp::rule<class expression> const expression("expression");  
  
    auto const uint = bp::uint_[do_int] | '(' >> expression >> ')';  
  
    auto const factor = uint  |  ('-' >> uint[do_neg]) | ('+' >> uint);  
  
    auto const term = factor >> *(('*' >> factor[do_mult]) |  
                            ('/' >> factor[do_div]));  
  
    auto const expression_def = term >> *(('+' >> term[do_add]) |  
                            ('-' >> term[do_subt]));  
  
    BOOST_PARSER_DEFINE_RULES(expression);  
  
    auto calculator = expression;  
}  
  
using calculator_grammar::calculator;  
  
}  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
int main()  
{  
    //test_recursive_node_visitor();  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Expression parser..." << std::endl << std::endl;  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
  
    std::string inputs[]=  
      { "999"  
      , "-888"  
      , "1+2"  
      , "2*3"  
      , "2*(3+4)"  
      , "2*-(3+4)"  
      };  
    for(std::string str:inputs) {  
  
        auto & calc = client::calculator; // Our grammar  
  
        auto r = bp::parse(str, calc, bp::ws);  
  
        if (r) {  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing succeeded" << std::endl;  
            assert(client::vn_stack.size() == 1);  
            std::cout << str << " ==> "  
            << std::visit(node_visitor(), client::vn_stack[0])  
            << " (AST eval)"  
            << std::endl;  
            client::vn_stack.pop_front();  
            std::cout << "-------------------------" << std::endl;  
        } else {  
            typedef std::string::const_iterator iterator_type;  
            iterator_type iter = str.begin();  
            iterator_type end = str.end();  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing failed" << std::endl;  
            bp::prefix_parse(iter, end, calc, bp::ws);  
            std::string rest(iter, end);  
            std::cout << "stopped at: \"" << rest << "\"" << std::endl;  
            client::vn_stack.clear();  
            std::cout << "-------------------------" << std::endl;  
        }  
    }  
  
    std::cout << "Bye... :-)" << std::endl << std::endl;  
    return 0;  
}  
  
```

---

## Comment 9

> Username: cppljevans  
> Created at: 2024-06-11 19:54:43 UTC  
> Updated at: 2024-06-12 00:24:19 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2161503577  

@LegalizeAdulthood, there's no need for the added complexity of vn_stack as demonstrated by:  
```c++  
/*=============================================================================  
Copyright (c) 2001-2014 Joel de Guzman  
  
Distributed under the Boost Software License, Version 1.0. (See accompanying  
file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
///////////////////////////////////////////////////////////////////////////////  
//  
//  A Calculator example demonstrating the grammar and semantic actions  
//  using lambda functions. The parser prints code suitable for a stack  
//  based virtual machine, the semantic actions create the AST and  
//  the evaluation is performed by a recursive visitor. The 'recursive' variant  
//  decoupling is done with the help of std::optional<>  
//  
//  [ JDG May 10, 2002 ]        spirit 1  
//  [ JDG March 4, 2007 ]       spirit 2  
//  [ JDG February 21, 2011 ]   spirit 2.5  
//  [ JDG June 6, 2014 ]        spirit x3 (from qi calc2, but using lambda functions)  
//  [ MPo Oct 27, 2022 ]        boost::parser (from spirit x3)  
//  [ MPo Apr 20, 2024 ]        AST, visitor  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <algorithm>  
#include <string>  
#include <deque>  
#include <limits>  
  
namespace bp = boost::parser;  
  
namespace client   
{  
    using val_t=int;  
    ///////////////////////////////////////////////////////////////////////////////  
    //  Semantic actions  
    ////////////////////////////////////////////////////////1///////////////////////  
    namespace   
    {  
  
    auto do_int = [](auto & ctx) {  
        val_t v = _attr(ctx);  
        std::cout << "do_int(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) = v;  
        std::cout << "do_int(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
    auto const do_add = [](auto & ctx) {  
        std::cout << "do_add(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) += bp::_attr(ctx);  
        std::cout << "do_add(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
    auto const do_subt = [](auto & ctx) {  
        std::cout << "do_subt(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) -= bp::_attr(ctx);  
        std::cout << "do_subt(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
    auto const do_mult = [](auto & ctx) {  
        std::cout << "do_mult(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) *= bp::_attr(ctx);  
        std::cout << "do_mult(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
    auto const do_div = [](auto & ctx) {  
        std::cout << "do_div(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) /= bp::_attr(ctx);  
        std::cout << "do_div(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
    auto const do_neg = [](auto & ctx) {  
        std::cout << "do_neg(before):_attr=" << bp::_attr(ctx) <<":_val="<<bp::_val(ctx)<< std::endl;  
        bp::_val(ctx) = -bp::_attr(ctx);  
        std::cout << "do_neg(after):_val="<<bp::_val(ctx)<< std::endl;  
    };  
  }//anonymous  
///////////////////////////////////////////////////////////////////////////////  
//  The calculator grammar  
///////////////////////////////////////////////////////////////////////////////  
namespace calculator_grammar  
{  
    using client::val_t  
      ;  
      template<typename Tag>  
    using calc_rule=  
      bp::rule<Tag,val_t>  
      ;  
    calc_rule<class expression_tag> const expression("expression");  
  
    calc_rule<class fac_uns_tag> const fac_uns("fac_uns");  
    auto const fac_uns_def   
      = bp::uint_[do_int]   
      | ('(' >> expression >> ')')[do_int]  
      ;  
    calc_rule<class factor_tag> const factor("factor");  
    auto const factor_def   
      = fac_uns[do_int]    
      | ('-' >> fac_uns[do_neg])   
      | ('+' >> fac_uns[do_int ])  
      ;  
    calc_rule<class term_tag> const term("term");  
    auto const term_def   
      = factor[do_int]  
       >> *( ('*' >> factor[do_mult])   
              | ('/' >> factor[do_div  ])  
              )  
      ;  
    auto const expression_def   
      = term[do_int]   
      >> *( ('+' >> term[do_add])   
            |  ('-' >> term[do_subt])  
            )  
      ;  
    BOOST_PARSER_DEFINE_RULES(expression,fac_uns,factor,term);  
  
    auto calculator = expression;  
  
}//calculator_grammar  
  
using calculator_grammar::calculator;  
  
}//client  
  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
int main()  
{  
    //test_recursive_node_visitor();  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Expression parser..." << std::endl << std::endl;  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
  
    std::string inputs[]=  
      { "999"  
      , "-888"  
      , "1+2"  
      , "2*3"  
      , "2*(3+4)"  
      , "2*-(3+4)"  
      };  
    for(std::string str:inputs) {  
  
        auto & calc = client::calculator; // Our grammar  
  
        client::val_t val{0};  
        auto r =   
          bp::parse  
          ( str  
          , calc  
          , bp::ws  
          , val  
          //, bp::trace::on  
          );  
  
        if (r) {  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing succeeded" << std::endl;  
            std::cout << "val = "<<val << std::endl;  
            std::cout << "-------------------------" << std::endl;  
        } else {  
            typedef std::string::const_iterator iterator_type;  
            iterator_type iter = str.begin();  
            iterator_type end = str.end();  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing failed" << std::endl;  
            bp::prefix_parse(iter, end, calc, bp::ws);  
            std::string rest(iter, end);  
            std::cout << "stopped at: \"" << rest << "\"" << std::endl;  
            std::cout << "-------------------------" << std::endl;  
        }  
    }  
  
    std::cout << "Bye... :-)" << std::endl << std::endl;  
    return 0;  
}  
```  
which produces expected output:  
```  
/////////////////////////////////////////////////////////  
  
Expression parser...  
  
/////////////////////////////////////////////////////////  
  
do_int(before):_attr=999:_val=0  
do_int(after):_val=999  
do_int(before):_attr=999:_val=0  
do_int(after):_val=999  
do_int(before):_attr=999:_val=0  
do_int(after):_val=999  
do_int(before):_attr=999:_val=0  
do_int(after):_val=999  
-------------------------  
Parsing succeeded  
val = 999  
-------------------------  
do_int(before):_attr=888:_val=0  
do_int(after):_val=888  
do_neg(before):_attr=888:_val=0  
do_neg(after):_val=-888  
do_int(before):_attr=-888:_val=0  
do_int(after):_val=-888  
do_int(before):_attr=-888:_val=0  
do_int(after):_val=-888  
-------------------------  
Parsing succeeded  
val = -888  
-------------------------  
do_int(before):_attr=1:_val=0  
do_int(after):_val=1  
do_int(before):_attr=1:_val=0  
do_int(after):_val=1  
do_int(before):_attr=1:_val=0  
do_int(after):_val=1  
do_int(before):_attr=1:_val=0  
do_int(after):_val=1  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_add(before):_attr=2:_val=1  
do_add(after):_val=3  
-------------------------  
Parsing succeeded  
val = 3  
-------------------------  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_mult(before):_attr=3:_val=2  
do_mult(after):_val=6  
do_int(before):_attr=6:_val=0  
do_int(after):_val=6  
-------------------------  
Parsing succeeded  
val = 6  
-------------------------  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_add(before):_attr=4:_val=3  
do_add(after):_val=7  
do_int(before):_attr=7:_val=0  
do_int(after):_val=7  
do_int(before):_attr=7:_val=0  
do_int(after):_val=7  
do_mult(before):_attr=7:_val=2  
do_mult(after):_val=14  
do_int(before):_attr=14:_val=0  
do_int(after):_val=14  
-------------------------  
Parsing succeeded  
val = 14  
-------------------------  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=2:_val=0  
do_int(after):_val=2  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=3:_val=0  
do_int(after):_val=3  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_int(before):_attr=4:_val=0  
do_int(after):_val=4  
do_add(before):_attr=4:_val=3  
do_add(after):_val=7  
do_int(before):_attr=7:_val=0  
do_int(after):_val=7  
do_neg(before):_attr=7:_val=0  
do_neg(after):_val=-7  
do_mult(before):_attr=-7:_val=2  
do_mult(after):_val=-14  
do_int(before):_attr=-14:_val=0  
do_int(after):_val=-14  
-------------------------  
Parsing succeeded  
val = -14  
-------------------------  
Bye... :-)  
```

---

## Comment 10

> Username: LegalizeAdulthood  
> Created at: 2024-06-12 20:51:40 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2163878704  

@cppljevans wrote:  
  
> Here's corrected code (with slight change in uint parser):  
  
...but now it has `2*(3+4)` and is missing `2+3*4` which would demonstrate the correct application of precedence.  
  
Basically, if you have to supply parentheses to enforce precedence, then you're not implementing precedence in the grammar, which is the original request.  I haven't had time to take your code and put it down somewhere and use it with the library.

---

## Comment 11

> Username: cppljevans  
> Created at: 2024-06-12 23:46:27 UTC  
> Updated at: 2024-06-21 23:53:55 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2164083735  

> @cppljevans wrote:  
>   
> > Here's corrected code (with slight change in uint parser):  
>   
> ...but now it has `2*(3+4)` and is missing `2+3*4` which would demonstrate the correct application of precedence.  
>   
> Basically, if you have to supply parentheses to enforce precedence, then you're not implementing precedence in the grammar, which is the original request. I haven't had time to take your code and put it down somewhere and use it with the library.  
  
Slightly revised code with requested test  
```  
/*=============================================================================  
Copyright (c) 2001-2014 Joel de Guzman  
  
Distributed under the Boost Software License, Version 1.0. (See accompanying  
file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
=============================================================================*/  
///////////////////////////////////////////////////////////////////////////////  
//  
//  A Calculator example demonstrating the grammar and semantic actions  
//  using lambda functions. The parser prints code suitable for a stack  
//  based virtual machine, the semantic actions create the AST and  
//  the evaluation is performed by a recursive visitor. The 'recursive' variant  
//  decoupling is done with the help of std::optional<>  
//  
//  [ JDG May 10, 2002 ]        spirit 1  
//  [ JDG March 4, 2007 ]       spirit 2  
//  [ JDG February 21, 2011 ]   spirit 2.5  
//  [ JDG June 6, 2014 ]        spirit x3 (from qi calc2, but using lambda functions)  
//  [ MPo Oct 27, 2022 ]        boost::parser (from spirit x3)  
//  [ MPo Apr 20, 2024 ]        AST, visitor  
//  
///////////////////////////////////////////////////////////////////////////////  
  
#include <boost/parser/parser.hpp>  
  
#include <iostream>  
#include <algorithm>  
#include <string>  
#include <deque>  
#include <limits>  
  
namespace bp = boost::parser;  
  
namespace client   
{  
    ///////////////////////////////////////////////////////////////////////////////  
    //  Semantic actions  
    ////////////////////////////////////////////////////////1///////////////////////  
  namespace//anonymous  
  {  
  
    auto do_copy = [](auto & ctx) {  
        bp::_val(ctx) = bp::_attr(ctx);  
    };  
    auto const do_neg = [](auto & ctx) {  
        bp::_val(ctx) = -bp::_attr(ctx);  
    };  
    auto const do_add = [](auto & ctx) {  
        bp::_val(ctx) += bp::_attr(ctx);  
    };  
    auto const do_subt = [](auto & ctx) {  
        bp::_val(ctx) -= bp::_attr(ctx);  
    };  
    auto const do_mult = [](auto & ctx) {  
        bp::_val(ctx) *= bp::_attr(ctx);  
    };  
    auto const do_div = [](auto & ctx) {  
        bp::_val(ctx) /= bp::_attr(ctx);  
    };  
  }//anonymous  
///////////////////////////////////////////////////////////////////////////////  
//  The calculator grammar  
///////////////////////////////////////////////////////////////////////////////  
namespace calculator_grammar  
{  
    using val_t=int  
      ;  
      template<typename Tag>  
    using calc_rule=  
      bp::rule<Tag,val_t>  
      ;  
    calc_rule<class expression_tag> const expression("expression");  
  
    calc_rule<class fac_uns_tag> const fac_uns("fac_uns");  
    auto const fac_uns_def   
      = bp::uint_[do_copy]   
      | ('(' >> expression >> ')')[do_copy]  
      ;  
    calc_rule<class factor_tag> const factor("factor");  
    auto const factor_def   
      = fac_uns[do_copy]    
      | ('+' >> fac_uns[do_copy])  
      | ('-' >> fac_uns[do_neg])   
      ;  
    calc_rule<class term_tag> const term("term");  
    auto const term_def   
      = factor[do_copy]  
      >> *( ('*' >> factor[do_mult])   
          | ('/' >> factor[do_div])  
          )  
      ;  
    auto const expression_def   
      = term[do_copy]   
      >> *(('+' >> term[do_add])   
          |('-' >> term[do_subt])  
          )  
      ;  
    BOOST_PARSER_DEFINE_RULES(expression,fac_uns,factor,term);  
  
    auto calculator = expression;  
  
}//calculator_grammar  
  
using calculator_grammar::calculator;  
  
}//client  
  
  
///////////////////////////////////////////////////////////////////////////////  
//  Main program  
///////////////////////////////////////////////////////////////////////////////  
int main()  
{  
    //test_recursive_node_visitor();  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
    std::cout << "Expression parser..." << std::endl << std::endl;  
    std::cout << "/////////////////////////////////////////////////////////"  
    << std::endl << std::endl;  
  
    std::string inputs[]=  
      { "999"  
      , "-888"  
      , "1+2"  
      , "2*3"  
      , "2+3*4"  
      , "2*(3+4)"  
      , "2*-(3+4)"  
      };  
    for(std::string str:inputs) {  
        std::cout<<"[===>str="<<str<<std::endl;  
        auto & calc = client::calculator; // Our grammar  
  
        client::calculator_grammar::val_t val{0};  
        auto r =   
          bp::parse  
          ( str  
          , calc  
          , bp::ws  
          , val  
          //, bp::trace::on  
          );  
  
        if (r) {  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing succeeded" << std::endl;  
            std::cout << "]===>val="<<val << std::endl;  
            std::cout << "-------------------------" << std::endl;  
        } else {  
            typedef std::string::const_iterator iterator_type;  
            iterator_type iter = str.begin();  
            iterator_type end = str.end();  
            std::cout << "-------------------------" << std::endl;  
            std::cout << "Parsing failed" << std::endl;  
            bp::prefix_parse(iter, end, calc, bp::ws);  
            std::string rest(iter, end);  
            std::cout << "stopped at: \"" << rest << "\"" << std::endl;  
            std::cout << "-------------------------" << std::endl;  
        }  
    }  
  
    std::cout << "Bye... :-)" << std::endl << std::endl;  
    return 0;  
}  
```  
  
with expected output:  
  
```  
/////////////////////////////////////////////////////////  
  
Expression parser...  
  
/////////////////////////////////////////////////////////  
  
[===>str=999  
-------------------------  
Parsing succeeded  
]===>val=999  
-------------------------  
[===>str=-888  
-------------------------  
Parsing succeeded  
]===>val=-888  
-------------------------  
[===>str=1+2  
-------------------------  
Parsing succeeded  
]===>val=3  
-------------------------  
[===>str=2*3  
-------------------------  
Parsing succeeded  
]===>val=6  
-------------------------  
[===>str=2+3*4  
-------------------------  
Parsing succeeded  
]===>val=14  
-------------------------  
[===>str=2*(3+4)  
-------------------------  
Parsing succeeded  
]===>val=14  
-------------------------  
[===>str=2*-(3+4)  
-------------------------  
Parsing succeeded  
]===>val=-14  
-------------------------  
Bye... :-)  
```  
  
To see details, uncomment the `bp::trace::on`.

---

## Comment 12

> Username: LegalizeAdulthood  
> Created at: 2024-06-25 16:06:48 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2189364563  

Thanks for the updated version!

---

## Comment 13

> Username: LegalizeAdulthood  
> Created at: 2025-06-06 14:32:52 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2949454015  

> Slightly revised code with requested test  
  
While I appreciate the updated version, I must say that I find this to be an inferior example compared to earlier versions.  One of the more difficult parts with Spirit and Parser is building a recursive AST correctly from the parsed input.  This last version does what I don't like about all the other "calculator" versions out there: it moves interpretation of the parsed inputs into the semantic actions and the AST disappears, so it becomes less useful as a real-world example.

---

## Comment 14

> Username: LegalizeAdulthood  
> Created at: 2025-06-06 14:57:45 UTC  
> Updated at: 2025-06-06 14:58:05 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2949528316  

Additional: I can see how this is doing everything by maintaining it's own attribute stack (`vn_stack`) and manipulating it through semantic actions, but I would really prefer to see an example using the attributes of the rules instead of maintaining everything "out of band" via semantic actions.  Yes, I can do whatever I want in semantic actions, but Boost.Parser already provides an elaborate attribute infrastructure and I'm just wasting that if I do everything out of band via semantic actions.  
  
How do we get Boost.Parser's attribute machinery to do the work for us of building the AST?

---

## Comment 15

> Username: LegalizeAdulthood  
> Created at: 2025-06-06 15:00:07 UTC  
> Updated at: 2025-06-06 15:00:35 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2949534373  

> I've never needed to implement a calculator.  
  
Think back to the 80s when we had Smalltalk environments and LISP machines.  Any time a number was requested, you could type in a math expression and get that evaluated to the number you want to input.  So instead of computing "`pi*3/4`" on your calculator and typing in the number, you just type in "`pi*3/4`" and let the computer do it.  This is something we lost over time as we "advanced".

---

## Comment 16

> Username: LegalizeAdulthood  
> Created at: 2025-06-09 15:08:02 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2956080560  

OK, I got there in the end using semantic actions to map the synthesized attribute to the appropriate type for my AST.  I _think_ I could have done it using the synthesized attributes only, e.g. `std::variant` for alternatives, `std::vector` for Kleene star and `std::tuple` for sequences. In the end I decided to use a polymorphic AST node structure and used semantic actions to create the appropriate nodes on the heap.  I'm not sure how you could do this entirely using synthesized attributes because the variant would end up being recursive and in order to forward declare things to unwind the recursive declaration, you need something like raw pointers or smart pointers.  Maybe I'm missing something here.  
  
[Permalink to parser](https://github.com/LegalizeAdulthood/asmjit-example/blob/63e0dd69a2e7b4ce8bb78bfee817b8105dfeb31c/libs/formula.cpp)  
  
As recommended in the docs (which are great, BTW, parsing is a complex topic and the docs do a great job of explaining how to tackle the complexity by explaining layers one at a time), unit-testing my code was indispensable in validating my implementation.

---

## Comment 17

> Username: cppljevans  
> Created at: 2025-06-14 02:24:12 UTC  
> Updated at: 2025-06-14 03:20:00 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2972140353  

> OK, I got there in the end using semantic actions to map the synthesized attribute to the appropriate type for my AST. I _think_ I could have done it using the synthesized attributes only, e.g. `std::variant` for alternatives, `std::vector` for Kleene star and `std::tuple` for sequences. In the end I decided to use a polymorphic AST node structure and used semantic actions to create the appropriate nodes on the heap. I'm not sure how you could do this entirely using synthesized attributes because the variant would end up being recursive and in order to forward declare things to unwind the recursive declaration, you need something like raw pointers or smart pointers. Maybe I'm missing something here.  
>   
> [Permalink to parser](https://github.com/LegalizeAdulthood/asmjit-example/blob/63e0dd69a2e7b4ce8bb78bfee817b8105dfeb31c/libs/formula.cpp)  
>   
> As recommended in the docs (which are great, BTW, parsing is a complex topic and the docs do a great job of explaining how to tackle the complexity by explaining layers one at a time), unit-testing my code was indispensable in validating my implementation.  
  
I'm puzzled by extensive use of std::make_shared.  The only place where that is needed is where the recursion occurs, and that  
only occurs with expr: https://github.com/LegalizeAdulthood/asmjit-example/blob/63e0dd69a2e7b4ce8bb78bfee817b8105dfeb31c/libs/formula.cpp#L178.  Furthermore, there's no need for make_shared for expr.  expr is not shared, it is a whole new expression.

---

## Comment 18

> Username: LegalizeAdulthood  
> Created at: 2025-06-14 22:48:18 UTC  
> Url: https://github.com/boostorg/parser/issues/141#issuecomment-2973307960  

> I'm puzzled by extensive use of std::make_shared.  
  
As I said, I'm using polymorphic nodes in my AST.  Without semantic actions to create the polymorphic nodes, Boost.Parser doesn't know how to make my node types.
