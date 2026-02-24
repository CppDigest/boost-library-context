# #249 - How to split parser into multiple compilation units? [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-16 13:55:09 UTC  
> Updated at: 2025-07-21 16:32:36 UTC  
> Closed at: 2025-07-20 21:19:45 UTC  
> Url: https://github.com/boostorg/parser/issues/249  

Hi,  
  
it seems like a very simple question, but I do not manage to do it.  
   
Compilation times get very slow as my parser grows, so I'd like to split my parser to multiple compilation units. So I am looking for some way to declare a parser without defining it.  
  
I could try to invent a new BOOST_PARSER_DECLARE_RULES similar to BOOST_PARSER_DEFINE_RULES. Then in addition I'd need a BOOST_PARSER_INSTANTIATE_RULES which instantiates them for a given iterator and skip parser type.  
  
What way would you recommend?  
  
Thanks a lot!  
Andreas

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-20 21:19:45 UTC  
> Url: https://github.com/boostorg/parser/issues/249#issuecomment-3094800892  

There's no equivalent of Spirit X3's BOOST_PARSER_INSTANTIATE_RULES, and one cannot be added.  
  
The way to get separate compilation is to write a function that takes concrete type(s) as its range of input, something like `result_type parse(range_type r)`, and then put all the Boost.Parser bits in the .cpp file.

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2025-07-21 16:32:36 UTC  
> Url: https://github.com/boostorg/parser/issues/249#issuecomment-3097485725  

Thanks for your reply.  
  
But I need to split *one* parser into multiple compilation units, not just move the one parser to a separate compilation unit. The compilation times just got out of hand.  
  
I now achieved it by  
  
1. Declare the rules in the header:  
```  
constexpr bp::rule<struct expression> expression = "expression";  
```  
2.  Declare the corresponding parse_rule functions in the header:  
```  
#define BOOST_PARSER_DECLARE_IMPL(_, rule_name_)                               \  
    template<                                                                  \  
        typename Iter,                                                         \  
        typename Sentinel,                                                     \  
        typename Context,                                                      \  
        typename SkipParser>                                                   \  
    decltype(rule_name_)::parser_type::attr_type parse_rule(                   \  
        decltype(rule_name_)::parser_type::tag_type *,                         \  
        Iter & first,                                                          \  
        Sentinel last,                                                         \  
        Context const & context,                                               \  
        SkipParser const & skip,                                               \  
        boost::parser::detail::flags flags,                                    \  
        bool & success,                                                        \  
        bool & dont_assign);                                                   \  
                                                                               \  
    template<                                                                  \  
        typename Iter,                                                         \  
        typename Sentinel,                                                     \  
        typename Context,                                                      \  
        typename SkipParser,                                                   \  
        typename Attribute>                                                    \  
    void parse_rule(                                                           \  
        decltype(rule_name_)::parser_type::tag_type *,                         \  
        Iter & first,                                                          \  
        Sentinel last,                                                         \  
        Context const & context,                                               \  
        SkipParser const & skip,                                               \  
        boost::parser::detail::flags flags,                                    \  
        bool & success,                                                        \  
        bool & dont_assign,                                                    \  
        Attribute & retval);  
  
  
#define BOOST_PARSER_DECLARE_RULES(...)                                         \  
    BOOST_PARSER_PP_FOR_EACH(BOOST_PARSER_DECLARE_IMPL, _, __VA_ARGS__)  
  
BOOST_PARSER_DECLARE_RULES(  
    expression  
    )  
  
```  
3. Instatiate whatever the linker complains about in the cpp file.
