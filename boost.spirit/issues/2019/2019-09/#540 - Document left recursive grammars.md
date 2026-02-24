# #540 - [X3] Document left recursive grammars [Open]

> Username: Xeverous  
> Created at: 2019-09-27 08:59:14 UTC  
> Updated at: 2025-05-10 01:30:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/540  

I have encountered the same problem multiple times.  
  
- start working on the current latest commit (where everything works and all my tests pass)  
- add more/extend grammar elements  
- run tests: tests crash or hang  
  
The problem appears when the grammar is wrongly designed which causes it to be defective by design. One such example is this:  
  
```c++  
auto const subscript_expression_def = value_expression >> '[' >> value_expression >> ']';  
BOOST_SPIRIT_DEFINE(subscript_expression)  
  
auto const value_expression_def =  
	  literal_expression  
	| array_expression  
	| subscript_expression // added most recently and defined above  
	| function_call  
	| identifier;  
BOOST_SPIRIT_DEFINE(value_expression)  
```  
  
The grammar for my `array_expression` accepts something like `[0, 1, 2, 3]`. Subscript will accept any expression followed by bracket-enclosed expression like `foo[0]`, `func()[0]`, `[0, 1, 2, 3][i]`.  
  
But this grammar does not actually work. When the parser encounters `foo[0]`, it does this:  
  
- parse it as value_expresion (go into this grammar)  
  - parse it as literal (fails, go to the next alternative)  
  - parse it as array expression (fails, go to the next alternative)  
  - parse it as subscript expression (go into this grammar)  
      - parse it as value_expression (first element of subscript expression)  
      - parse it as literal (fails, go to the next alternative)  
      - parse it as array expression (fails, go to the next alternative)  
      - parse it as subscript expression (go into this grammar)  
          - (infinite loop/recursion without consuming any characters)  
  
Obviously this is a bug in the design/implementation which can be fixed by different order of alternatives but I'm interested if:  
  
- **Would it be possible to detect such "malformed" grammars at compile-time?** All of such malformed grammars share the same pattern: 2 grammars contain each other as one of their root-level alternatives and there are no fixed character/keyword tokens between them, which allows infiite loops/recursion without consuming any input.  
- **Should Spirit have some guidance in grammar/language design on its documentation?** Currently it covers how to use the library, but I think this is important for a frustration-free project. https://en.wikipedia.org/wiki/LL_parser#LL(1)_Conflicts has some good examples, eg replace `x | (x >> y >> z)` with `x >> -(y >> z)`, avoid left recursion that does not consume any characters etc

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-09-29 14:41:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/540#issuecomment-536308273  

> The problem appears when the grammar is wrongly designed which causes it to be defective by design.  
  
Those grammars are not defective, but just left recursive (as further noted by you), and Spirit being a recursive descent parser cannot parse such grammars  
https://en.wikipedia.org/wiki/Left_recursion#Accommodating_left_recursion_in_top-down_parsing.  
  
> Would it be possible to detect such "malformed" grammars at compile-time?  
  
It is possible for context-free grammars, as also possible to rewrite them, though it will certainly break at least some usages.  
  
> Should Spirit have some guidance in grammar/language design on its documentation?  
  
I do not think copying Wikipedia or any other source is a good idea, not only because of probable legal problems with such actions, but because there are different left recursion elimination algorithms and optimizations on them.  
  
Spirit.Classic documentation has a record in FAQ about left recursion https://www.boost.org/doc/libs/master/libs/spirit/classic/doc/faq.html#left_recursion, I am not sure about its usefulness, but you can improve it.

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-09-29 17:04:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/540#issuecomment-536321060  

> Spirit.Classic documentation has a record in FAQ about left recursion https://www.boost.org/doc/libs/master/libs/spirit/classic/doc/faq.html#left_recursion, I am not sure about its usefulness, but you can improve it.  
  
I think it is useful, it showcases examples how to deal with common left recursion problems. Could be updated and added to X3 docs.

---

## Comment 3

> Username: djowel  
> Created at: 2019-09-30 03:36:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/540#issuecomment-536387613  

I welcome any PR for improving the docs.
