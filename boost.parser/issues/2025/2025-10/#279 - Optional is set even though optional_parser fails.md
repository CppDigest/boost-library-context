# #279 - Optional is set even though optional_parser fails [Closed]

> Username: andreasbuhr  
> Created at: 2025-10-06 11:51:29 UTC  
> Updated at: 2025-10-14 14:44:47 UTC  
> Closed at: 2025-10-13 22:47:26 UTC  
> Url: https://github.com/boostorg/parser/issues/279  

In the following code, parsing a do-statement with a condition (heavily simplified), the std::optional representing the condition should not be set. However, it is. This is a bug in boost.parser.  
  
```c++  
void reproducer()  
{  
    namespace bp = boost::parser;  
  
    constexpr auto condition_clause =  
        bp::lit(U"while") > bp::lit(U"someexpression") >> bp::attr(true) ;  
  
    constexpr auto do_statement =  
        bp::lexeme[bp::lit(U"do") >> &bp::ws] > -condition_clause > bp::eol;  
  
  
    {  
        auto result = bp::parse(  
            U"do\n"  
            , do_statement, bp::blank, bp::trace::off);  
        BOOST_TEST(result);  
        std::optional<bool>& condition = result.value();  
        BOOST_TEST(!condition.has_value());  
    }  
}  
```

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-10-14 14:44:47 UTC  
> Url: https://github.com/boostorg/parser/issues/279#issuecomment-3402230213  

While the reproducer now works, I think the problem is not fundamentally solved.  
  
The fundamental problem is that `bp::lexeme[bp::lit(U"do") >> &bp::ws]` tinkers with the std::optional it is given, even though it has no business doing so.  
Your fix was to make sure the opt_parser writes to the attribute in any case. So it overwrites the wrong values written to the std::optional by `bp::lexeme[bp::lit(U"do") >> &bp::ws]`. But this fix only works if the `bp::lexeme[bp::lit(U"do") >> &bp::ws]` comes before the optional parser.  
  
If the `bp::lexeme[bp::lit(U"do") >> &bp::ws]` comes after the optional parser, the problem is still there. The opt_parser defined by by `-condition_clause` writes correctly an empty std::optional<bool>. The `bp::lexeme[bp::lit(U"do") >> &bp::ws]` comes along an default-constructs a "bool" into the std::optional<bool>.  
  
I extended the reproducer in https://github.com/boostorg/parser/pull/283
