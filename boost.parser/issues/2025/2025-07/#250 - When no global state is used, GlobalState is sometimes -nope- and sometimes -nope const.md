# #250 - When no global state is used, GlobalState is sometimes "nope" and sometimes "nope const" [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-26 18:32:13 UTC  
> Updated at: 2025-10-13 01:43:39 UTC  
> Closed at: 2025-10-13 01:43:39 UTC  
> Url: https://github.com/boostorg/parser/issues/250  

Hi,  
  
I am not using any global state.  
When I look at the binary generated, using "readelf -WsC" or the like, I see that for the same parser, parse_rule is often instantiated twice with the only difference that the GlobalState used in the Context is once "nope" and the other time "nope const".  
  
This leads to higher compilation times than necessary and larger binary sizes than necessary. It would be nice if always "nope const" was used, or always "nope".  
  
So far I did not figure out where exactly the GlobalState in the Context changes from "nope const" to "nope". Rules which are used from bp::parse directly seem to get "nope const" as GlobalState in their Context. Somewhere down the tree it changes to "nope" as GlobalState in the Context.  
  
It seems to happen with gcc 14.2. I was not able to reproduce it with clang20 and MSVC2022.  
  
I have a reproducer on godbolt here: https://godbolt.org/z/T9nT83h7h  
Search for "nope const" in the assembly window.  
  
Here are two example signatures. I know it's hard to spot.  
```  
MyAttributeType parse_rule<boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::default_error_handler, boost::parser::detail::nope const, boost::parser::detail::nope, boost::parser::detail::nope, MyAttributeType, literal_expr_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::parser_interface<boost::parser::rule_parser<false, WS_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::detail::nope, boost::parser::default_error_handler> >(literal_expr_tag*, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>&, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::default_error_handler, boost::parser::detail::nope const, boost::parser::detail::nope, boost::parser::detail::nope, MyAttributeType, literal_expr_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope> const&, boost::parser::parser_interface<boost::parser::rule_parser<false, WS_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::detail::nope, boost::parser::default_error_handler> const&, boost::parser::detail::flags, bool&, bool&)  
  
  
MyAttributeType parse_rule<boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, MyAttributeType, literal_expr_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope>, boost::parser::detail::null_parser>(literal_expr_tag*, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>&, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::parse_context<false, false, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::detail::text::utf_iterator<(boost::parser::detail::text::format)1, (boost::parser::detail::text::format)4, char8_t const*, char8_t const*, boost::parser::detail::text::use_replacement_character>, boost::parser::default_error_handler, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope, MyAttributeType, literal_expr_tag, boost::parser::detail::nope, boost::parser::detail::nope, boost::parser::detail::nope> const&, boost::parser::detail::null_parser const&, boost::parser::detail::flags, bool&, bool&)  
```

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-07-26 19:07:58 UTC  
> Url: https://github.com/boostorg/parser/issues/250#issuecomment-3122536057  

Ah sorry, this was nonsense, they also differ by the whitespace parser later down the line. I was confused.

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2025-07-26 19:20:54 UTC  
> Url: https://github.com/boostorg/parser/issues/250#issuecomment-3122641021  

No, I am still confused. The same issue arises with the exact same whitespace parser.  
I do have instantiations of parse_rule which only differ by the "nope" vs. "nope const" in the GlobalState of the Context.
