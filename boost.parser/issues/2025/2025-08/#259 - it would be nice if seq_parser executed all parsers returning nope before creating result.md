# #259 - it would be nice if seq_parser executed all parsers returning nope before creating result [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-04 15:40:47 UTC  
> Updated at: 2025-10-13 01:13:34 UTC  
> Closed at: 2025-10-13 01:13:34 UTC  
> Url: https://github.com/boostorg/parser/issues/259  

Hi,  
  
sometimes, the result of a seq_parser is quite heavy to create, but the parser already fails in the first subparser, which returns nope. In that case the result structure was created, but never used. I have this case quite often when I have parsers like  
```  
constexpr auto my_parser_def = bp::lit("somekeyword") >> some_other_stuff >> more_other_stuff;  
```  
It would give better performance if seq_parser did the following:  
  
1. Execute all parsers until the first parser not returning nope.  
2. Create the result structure  
3. Execute all remaining parsers  
  
I tried simulating this by doing the following  
```  
constexpr auto my_parser_def = &bp::lit("somekeyword") >> (bp::lit("somekeyword") >> some_other_stuff >> more_other_stuff);  
```  
But it seems that boost::parser outsmarts me and merges them into one big seq_parser, which again creates the result before executing the "&bp::lit" parser.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:13:34 UTC  
> Url: https://github.com/boostorg/parser/issues/259#issuecomment-3395580193  

This kind of thing can be accomplished using rules rather than subparsers, because the collapsing logic cannot see through calls to rules' parsers.  You can also accomplish this by making a rule that has multiple scratch vars and a parser that parses into each scratch var -- and only constructing the final result in a semantic action at the end of the rule.  This kind of control of when attributes are generated is already in users' control.
