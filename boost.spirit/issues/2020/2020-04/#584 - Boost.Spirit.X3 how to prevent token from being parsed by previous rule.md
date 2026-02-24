# #584 - Boost.Spirit.X3 how to prevent token from being parsed by previous rule? [Closed]

> Username: Abdullah-AlAttar  
> Created at: 2020-04-08 07:34:15 UTC  
> Updated at: 2020-04-12 13:25:03 UTC  
> Closed at: 2020-04-12 13:25:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/584  

given this grammar :  
```cpp  
const auto grammar_def = x3::lit("start")  
                        > x3::lit("{")  
                        > (*(char_("a-zA-Z0-9\".{}=_~")))  
                        > x3::lit("}") > x3::lit(";);  
```  
the last **x3::lit("}")** is being consumed by **(*(char_("a-zA-Z0-9\".{}=_~")))** since it contains **"}"**    
is there way to prevent that ? something like giving **x3::lit("}")** higher priority during parse?

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-04-12 13:25:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/584#issuecomment-612614041  

[No, there is not](https://www.boost.org/doc/libs/1_72_0/libs/spirit/doc/html/spirit/abstracts/parsing_expression_grammar.html#spirit.abstracts.parsing_expression_grammar.loops).  
  
Use Spirit over tokenized input by a non-backtracking regex engine if you really want to match this kind of thing with a consistent performance.
