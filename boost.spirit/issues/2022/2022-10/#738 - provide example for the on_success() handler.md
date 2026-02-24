# #738 - provide example for the on_success() handler [Open]

> Username: doj  
> Created at: 2022-10-04 00:55:16 UTC  
> Updated at: 2025-05-09 23:26:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/738  

https://www.boost.org/doc/libs/1_80_0/libs/spirit/doc/html/spirit/qi/tutorials/mini_xml___error_handling.html mentions the on_success() handler, but the handler is not used in the example shown on this page. It is also not used in the full example source code https://www.boost.org/doc/libs/1_80_0/libs/spirit/example/qi/mini_xml3.cpp  
  
It will be useful to show the on_success() handler in this example, ideally how the iterator parameters can be used to calculate line and column numbers of the input stream.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-10-05 23:03:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/738#issuecomment-1269083981  

Documentation improvements are highly appreciated!  
  
Note that Qi is in maintenance mode, it is preferred to write new parsers with X3.

---

## Comment 2

> Username: djowel  
> Created at: 2022-10-05 23:11:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/738#issuecomment-1269090047  

> Documentation improvements are highly appreciated!  
  
I second that.

---

## Comment 3

> Username: drexlerd  
> Created at: 2023-11-08 17:52:19 UTC  
> Updated at: 2023-11-09 09:16:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/738#issuecomment-1802377202  

Edit: it is also possible return fail in the on_success callback via `_pass(context) = false`.  
  
I also would like to see more examples, especially when parsing context-sensitive languages. I am parsing a context-sensitive language and therefore, I would need to reject successful parses depending on what is stored in the Context. My approach would be to provide template specializations of the on_success callback for specific ast nodes and then throw an expectation_failure if I want to reject. Is this a reasonable approach when parsing a context-sensitive language with x3?

---

## Comment 4

> Username: cppljevans  
> Created at: 2023-11-17 12:59:35 UTC  
> Updated at: 2023-11-18 03:35:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/738#issuecomment-1816381087  

> Edit: it is also possible return fail in the on_success callback via `_pass(context) = false`.  
>   
> I also would like to see more examples, especially when parsing context-sensitive languages. I am parsing a context-sensitive language and therefore, I would need to reject successful parses depending on what is stored in the Context. My approach would be to provide template specializations of the on_success callback for specific ast nodes and then throw an expectation_failure if I want to reject. Is this a reasonable approach when parsing a context-sensitive language with x3?  
  
[This example](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp) shows 2 methods.  Which method is decided by defined(USE_SEMANTIC_ACTIONS) preprocessor macro.  When defined, it uses semantic actions which obscure the grammar with the semantic action expressions.  When !defined, it uses a single line expression, as_attr, in the Parser grammar  
which delegates the handling of the context sensitivity to the [transform_attribute_tagged](https://github.com/cppljevans/boost.exploratory/blob/main/boost.replacements/examples/realworld/spirit-issues/issue749.x3-html/coliru_sehe-debug-simple-compare.cpp#L263) which is much cleaner.  Maybe you could throw the expectation_failure in the [post function]  
  
OTOH [this](  
https://hypertextbookshop.com/transPL/Contents/01_Topics/04_Symbol_Tables/01_Section_1/01_page_1_-_Symbol_Table_Overview.html) says symbol tables are very useful for context sensitive parsers and semantic actions are one way to update the symbol table, hence, maybe the above suggested use of as_attr instead of semantic actions wouldn't work too well.  
  
On the Other Other Hand, symbol tables are usually passed as inherited attributes, according to [this]( https://www.sciencedirect.com/topics/computer-science/inherited-attribute), and x3 does not have inherited attributes.  I'd assume inherited attribute could be simulated by storing the symbol table in the context.
