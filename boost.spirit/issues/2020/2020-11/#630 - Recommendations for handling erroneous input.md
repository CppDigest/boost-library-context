# #630 - Recommendations for handling erroneous input [Closed]

> Username: Xeverous  
> Created at: 2020-11-30 00:21:28 UTC  
> Updated at: 2021-06-01 19:23:43 UTC  
> Closed at: 2021-06-01 19:23:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/630  

I have been using X3 for a while and I have trouble deciding where to handle invalid input so I'm asking for recommendations - perhaps the library authors have more experience. I haven't found any guidelines for this in the documentation. I think problems like this one (and many general recommendations) could be a worthwhile addition to the documentation - the library is focused on a very narrow topic and I predict not all library users are very knowledgeable in this area.  
  
The problem is as follows: the input contains invalid tokens, but they can be someway dealt with. For example, if a C++ compiler sees `int x = foo();` and there is no definition of `foo`, it can issue an error and still continue because it knows the type of `x` so further code is not a problem. At worst punctuation problems it can skip to `;` and assume that a statement did not exist.  
  
I'm having similar issue: I have a lot of keyword-like elements in the grammar but I'm not sure if:  
  
A) Should I make the grammar very precise and fail the parsing upon any slight error in the grammar? (That is, keep it as a parse error)  
B) Should I make the grammar more flexible by allowing all kinds of literals that my "language" supports and then output errors in code that traverses the complete AST? (That is, turn parsing errors into semantic errors by making the grammar more flexible)  
  
For example, let's say that in some text language format such lines are valid:  
  
```  
SetColor 255 0 255 255  
SetColor 255 0 255  
SetColor 0xff00ff  
SetColor 0xff00ffff  
SoundEffect true  
SoundEffect false  
```  
  
A (precise grammar version)  
  
```cpp  
auto const color_statement =  
    "SetColor" >> (int_ >> int_ >> int_ >> -int_) | lexeme["0x" >> hex_char_table] >> eol;  
```  
  
B (loose grammar with more semantic analysis required)  
  
```cpp  
auto const token = boolean_keywords_table | lexeme["0x" >> hex_char_table] | int_;  
auto const color_statement = "SetColor" >> +token >> eol;  
```  
  
In the case of A, parsing will fail much more often on any input problem and there will be many complex subgrammars for each "command".  
  
In the case of B, parsing will often succeed; even if the input had something like `SetColor 255 255` or `SetColor true` but there will be very few complex grammars as most will utilize the `+token >> eol` part, making the parser simpler but requiring more code to work with the AST.  
  
Is there any general recommendation for this type of problems or it is so case-specific that I better use my own judgement? Handling semantic errors is much easier for me than syntaxic ones, and in case of semantic errors it's much more likely to make the code go forward because fixing/faking semantic state is way less complex than fixing parser's position and adjacent AST nodes.  
  
Last thing, if B is better, how about such grammars?  
  
```cpp  
auto const broken_token = *(char_ - eol);  
auto const token = boolean_keywords_table | lexeme["0x" >> hex_char_table] | int_ | broken_token;  
```  
  
In this extreme case of flexibility, the program can accept a huge variety of invalid inputs and the code that works with complete AST will always error upon `broken_literal` but then we have the highest likehood of being able to continue. I have no idea whether always-invalid-things such as `broken_literal` are a good design though.

---

## Comment 1

> Username: djowel  
> Created at: 2020-11-30 01:03:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/630#issuecomment-735491422  

Have you seen this?  
  
https://www.boost.org/doc/libs/1_68_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html

---

## Comment 2

> Username: Xeverous  
> Created at: 2020-11-30 09:51:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/630#issuecomment-735678231  

Yes, but the page does not mention anything about delaying error handling to later phases. I know how I can handle parse errors, I'm more concerned whether they should be parse errors if I can make the grammar more flexible by accepting wider set of tokens and then issue errors in later phases.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-03-15 19:01:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/630#issuecomment-799674744  

The questions you are asking shows that you are moving in the right direction. Unfortunately, I cannot give you a silver bullet, the topic is complex and any solutions are use-case based. You probably should reach out to researches (or researchers) in this area.  
  
> the input contains invalid tokens  
  
That is a both a strong and a weak point of lexer-based parsers, but I seems like you used word `tokens` in a different meaning. You are not using a tokenizer and using a word `token` here and after is confusing.  
  
> At worst punctuation problems it can skip to `;` and assume that a statement did not exist.  
  
For future references, I know this as recovery points.  
  
> A) Should I make the grammar very precise and fail the parsing upon any slight error in the grammar? (That is, keep it as a parse error)  
  
As you already noticed, this is what you get by default when you implement a parser, as it is intrinsically a validating parser. In most use-cases, when you are dealing with machine generated content, it is all what you need.  
  
> B) Should I make the grammar more flexible by allowing all kinds of literals that my "language" supports and then output errors in code that traverses the complete AST? (That is, turn parsing errors into semantic errors by making the grammar more flexible)  
  
I think you gone a little bit too far here, almost turning a parser into a lexer/tokenizer, but there is middle ground. You will see what I mean in a moment.  
  
> A (precise grammar version)  
>   
> ```c++  
> auto const color_statement =  
>     "SetColor" >> (int_ >> int_ >> int_ >> -int_) | lexeme["0x" >> hex_char_table] >> eol;  
> ```  
>   
> B (loose grammar with more semantic analysis required)  
>   
> ```c++  
> auto const token = boolean_keywords_table | lexeme["0x" >> hex_char_table] | int_;  
> auto const color_statement = "SetColor" >> +token >> eol;  
> ```  
  
Surely, you code in B will contain A somewhere after, in an AST visitation step. It is also will not handle any input, making this solution questionable. There already was an 'alternative' parser, so I am not sure why you have not considered yourself adding an error recovery branch here.  
  
```c++  
auto const color_statement =  
    "SetColor" >> (int_ >> int_ >> int_ >> -int_ | lexeme["0x" >> hex_char_table] | error_handling_branch) >> eol;  
```  
  
I do not know the details of your grammar, but `eol` in this case looks like an excellent recovery point, making error recovery as easy as `seek[eol]`. In the `error_handling_branch` you can do `raw[seek[eol]][report_error]`, which will advance the parser to an recovery point, report an parsing error, and if your AST node value for `SetColor` already a variant, will set it to some kind of an 'invalid' state.  
  
In case if you really need to delay an error reporting until later, there is a more advanced technique that Clang does in its new way to present incomplete parsing information https://clang.llvm.org/docs/InternalsManual.html#recovery-ast.

---

## Comment 4

> Username: Xeverous  
> Created at: 2021-03-15 22:40:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/630#issuecomment-799802654  

Wow, I haven't expected a response after so much time but nonetheless I am satisfied.  
  
> Surely, you code in B will contain A somewhere after, in an AST visitation step.  
  
This is a very good observation, I haven't noticed it.  
  
> There already was an 'alternative' parser, so I am not sure why you have not considered yourself adding an error recovery branch here.  
  
This is the thing that I have missed. I have considered more loose grammars (which more or less end up as tokenizers, wasting Spirit potential) and more strict grammars (which stop on smallest error but complicate any diagnostics production). I have thought about "broken-parsed-element" thing but I haven't realized that in such cases I should advance the parser to some recovery point.  
  
> advance the parser to an recovery point, report an parsing error, and if your AST node value for SetColor already a variant, will set it to some kind of an 'invalid' state.  
  
This actually seems to be a silver bullet: **insert parser recoveries as last variant alternatives**. Alternatives within the grammar, due to their nature will usually have some recovery point close to them which makes it a very good place to recover from. And if something fails elsewhere, it will move upward the grammar untill it reaches an alternative. Thus, I feel like with this approach there is a very high chance of producing high-quality errors with very high chance to be able to continue parsing.  
  
> In case if you really need to delay an error reporting until later, there is a more advanced technique that Clang does in its new way to present incomplete parsing information  
  
This is a lot of info but I got the point: insert a broken-parsed-element that will contain some state, potentially an entire sub-AST but the whole thing is considered an error because it is the broken-parsed-element variant alternative.  
  
Thanks a lot!

---

## Comment 5

> Username: cppljevans  
> Created at: 2021-03-18 10:34:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/630#issuecomment-801811769  

Web searching for:  
  
   computer science LL1 parser error recovery using follow sets  
  
shows a lot of hits.  This one:  
  
https://www.cs.clemson.edu/course/cpsc827/material/LLk/LL%20Error%20Recovery.pdf  
  
mentions FOLLOW(A) sets for a non-terminal, A, as, what I assume,  
amounts to a "recovery point".  Unfortunately, the only algorithm I know  
of requires traversing, recursively, the grammar to calculate the  
FOLLOW(A) set for each non-terminal, A.  Maybe spirit-x3 can be  
adapted to do that, but I'd think it would be prohibitively  
compile-time expensive.  It could maybe be done in a separate program  
run and the calculated FOLLOW(A)'S then somehow #include'd in another  
spirit program, say x3-follow, or some similar name, but that's very  
speculative.  Of course you could "eye-ball" the FOLLOW(A) for any  
give non-terminal, A, but if A is complicated, that would likely be  
very error prone.  
  
Sorry can't be more help.  
  
-Larry  
  
On 3/15/2021 5:40 PM, Xeverous wrote:  
>  
> Wow, I haven't expected a response after so much time but nonetheless   
> I am satisfied.  
>  
>     Surely, you code in B will contain A somewhere after, in an AST  
>     visitation step.  
>  
> This is a very good observation, I haven't noticed it.  
>  
>     There already was an 'alternative' parser, so I am not sure why  
>     you have not considered yourself adding an error recovery branch here.  
>  
> This is the thing that I have missed. I have considered more loose   
> grammars (which more or less end up as tokenizers, wasting Spirit   
> potential) and more strict grammars (which stop on smallest error but   
> complicate any diagnostics production). I have thought about   
> "broken-parsed-element" thing but I haven't realized that in such   
> cases I should advance the parser to some recovery point.  
>  
>     advance the parser to an recovery point, report an parsing error,  
>     and if your AST node value for SetColor already a variant, will  
>     set it to some kind of an 'invalid' state.  
>  
> This actually seems to be a silver bullet: *insert parser recoveries   
> as last variant alternatives*. Alternatives within the grammar, due to   
> their nature will usually have some recovery point close to them which   
> makes it a very good place to recover from. And if something fails   
> elsewhere, it will move upward the grammar untill it reaches an   
> alternative. Thus, I feel like with this approach there is a very high   
> chance of producing high-quality errors with very high chance to be   
> able to continue parsing.  
>  
>     In case if you really need to delay an error reporting until  
>     later, there is a more advanced technique that Clang does in its  
>     new way to present incomplete parsing information  
>  
> This is a lot of info but I got the point: insert a   
> broken-parsed-element that will contain some state, potentially an   
> entire sub-AST but the whole thing is considered an error because it   
> is the broken-parsed-element variant alternative.  
>  
> Thanks a lot!  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/630#issuecomment-799802654>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBAZSSB3DIGDZVC2WVG3TD2EGTANCNFSM4UG32D7A>.  
>
