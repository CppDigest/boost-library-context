# #177 - KISS for rule_id to rule RHS macro [Closed]

> Username: cppljevans  
> Created at: 2024-05-26 02:55:53 UTC  
> Updated at: 2024-09-30 20:36:10 UTC  
> Closed at: 2024-09-30 20:36:10 UTC  
> Url: https://github.com/boostorg/parser/issues/177  

Zach,   
  
AFAICT the [BOOST_PARSER_DEFINE_IMP_macro](https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L5741)  
performs, "essentially" the same purpose as the  
[BOOST_SPIRIT_DEFINE_ macro](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/rule.hpp#L228).  
  
Now, again, AFAICT, the purpose of those macros is to "associate" the  
rule_id with the rules RHS definition.  However, this "association" is  
performed **MUCH** more simply by [the simplified BOOST_SPIRIT_DEFINE_ macro](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L90)  
in combination with the modified [out-of-macro definition of parse_rule](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L90).  
which  
[uses](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L33)  
the `rule_defn` specialization defined by the `simplified BOOST_SPIRIT_DEFINE_`.  
  
Hence, I suggest adopting, with any appropriate  
adapation, the simpler macro in order to conform to the [KISS  
principal](https://en.wikipedia.org/wiki/KISS_principle).

---

## Comment 1

> Username: cppljevans  
> Created at: 2024-06-05 17:54:59 UTC  
> Url: https://github.com/boostorg/parser/issues/177#issuecomment-2150633850  

> Zach,  
>   
> AFAICT the [BOOST_PARSER_DEFINE_IMP_macro](https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L5741) performs, "essentially" the same purpose as the [BOOST_SPIRIT_DEFINE_ macro](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/rule.hpp#L228).  
>   
> Now, again, AFAICT, the purpose of those macros is to "associate" the rule_id with the rules RHS definition. However, this "association" is performed **MUCH** more simply by [the simplified BOOST_SPIRIT_DEFINE_ macro](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L90) in combination with the modified [out-of-macro definition of parse_rule](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L90). which [uses](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/rule_defn/boost/spirit/home/x3/nonterminal/parse_rule.hpp#L33) the `rule_defn` specialization defined by the `simplified BOOST_SPIRIT_DEFINE_`.  
>   
> Hence, I suggest adopting, with any appropriate adapation, the simpler macro in order to conform to the [KISS principal](https://en.wikipedia.org/wiki/KISS_principle).  
  
The following gist implements the suggested changes when defined(USE_TAG_TO_DEF): [revised parser.hpp](https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284).

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-09-29 20:34:46 UTC  
> Url: https://github.com/boostorg/parser/issues/177#issuecomment-2381593752  

This doesn't seem any simpler to me.  Here, we name the expected attribute type at the call site, but that may or may not be returned, because you changed the return type of `parse_rule()` to `auto`.  This means any conversions, promotions, etc., to the expected attribute type don't happen.  As for the associated function, this will take longer at compile time (it will have to resolve one extra templated function call) over the status quo.  Am I missing something?

---

## Comment 3

> Username: cppljevans  
> Created at: 2024-09-30 07:05:47 UTC  
> Url: https://github.com/boostorg/parser/issues/177#issuecomment-2382278797  

> This doesn't seem any simpler to me. Here, we name the expected attribute type at the call site, but that may or may not be returned, because you changed the return type of `parse_rule()` to `auto`. This means any conversions, promotions, etc., to the expected attribute type don't happen. As for the associated function, this will take longer at compile time (it will have to resolve one extra templated function call) over the status quo. Am I missing something?  
  
However, without question, this macro:  
  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5751  
  
is simpler than the corresponding macro:  
  
  https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L5741  
      
Furthermore, although it's true:  
  
  "you changed the return type of parse_rule() to auto"  
    
here:  
  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5763  
    
the returns from that function are either explicity the rule's "expected  
attribute type"(a.k.a attr_type):  
  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5784  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5789    
  
or the attribute type of the rule's definition(a.k.a attr_t):  
  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5780  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5782  
  
and since the documentation:  
  
  https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html  
  
requires(with no semantic actions):  
  
  - ATTR(R) and ATTR(P) are compatible types  
  
where R is the rule and P is the rule's defintion, we can assume that  
the attribute type of the rule's definition(a.k.a attr_t) is "compatible" with the  
rule's "expected attribute type"(a.k.a attr_type), there should be no  
problem of "conversions" 'to the "expected attribute type"' in the  
`parse_rule` here:  
  
  https://gist.github.com/cppljevans/4fd755af084f3623c8d1b5d9d0b15284#file-parser-hpp-L5763  
  
Furthermore, the issue of **slightly** more complex compile times does not, IMO,  
counterbalance the increased clarity of the code accomplished with the  
proposed simplification.  
      
If I'm missing something, please provide a test case showing that I'm  
missing that something.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-09-30 20:36:10 UTC  
> Url: https://github.com/boostorg/parser/issues/177#issuecomment-2384099749  

Most users will not want to pay for longer compiles so that code that they never look at is marginally easier to read.  Moreover, your suggested change makes the code seem simpler to you, but adding another function to the mix is not a simplification to me.  Since the two are equivalent semantically, I see no need to make this change.
