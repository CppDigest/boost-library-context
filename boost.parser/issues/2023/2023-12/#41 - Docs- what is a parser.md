# #41 - Docs: what is a parser? [Closed]

> Username: akrzemi1  
> Created at: 2023-12-31 14:34:55 UTC  
> Updated at: 2024-01-08 23:51:45 UTC  
> Closed at: 2024-01-08 04:55:25 UTC  
> Url: https://github.com/boostorg/parser/issues/41  

In Boost.Parser, what is considered a parser? Is it:  
* a type that derives from `parser_interface`?  
* any type that has the required interface?   
  
It looks like it is the former. I recommend devoting a section to Parser. It would say:  
* that a parser is a _CRTP-specialization of_ `parser_interface`,  
* when (if) one has to provide their own CRTP-specialization  
* what the iterface of `parser_interface` is.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-08 15:29:26 UTC  
> Url: https://github.com/boostorg/parser/issues/41#issuecomment-1881231764  

Can you specify what it means to be a parser as a c++ concept (even if you do not use concepts in the implementation)?  
  
If I define a class that only has the two call operators and nothing else, will it work as a parser with your library?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-08 21:40:46 UTC  
> Url: https://github.com/boostorg/parser/issues/41#issuecomment-1881865334  

It looks to me that when I need to create a parser:  
* I must declare the two function call operators, and  
* I mast inherit from `parser_interface`.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-08 21:46:17 UTC  
> Url: https://github.com/boostorg/parser/issues/41#issuecomment-1881871859  

In section [Parsing In Detail](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/parsing_in_detail.html), I do not understand this sentence:  
  
> Step 1) The input is "1 2 3", and the stack of active parsers is `int_pairs_plus` -> `+int_pair` -> `int_pair` -> `bp::int_`.   
  
Is this a mistake? Or is it correct? But what does the arrow represent here? Does this mean a four-element stack?

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-08 23:50:55 UTC  
> Url: https://github.com/boostorg/parser/issues/41#issuecomment-1881999673  

> In section [Parsing In Detail](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/parsing_in_detail.html), I do not understand this sentence:  
>   
> > Step 1) The input is "1 2 3", and the stack of active parsers is `int_pairs_plus` -> `+int_pair` -> `int_pair` -> `bp::int_`.  
>   
> Is this a mistake? Or is it correct? But what does the arrow represent here? Does this mean a four-element stack?  
  
Yes, this is right.  I'll add more text to make it clearer what I mean.  Bascially, since each parser is a callable, there is a stack of parsers from the top-level one down to the current parser.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-01-08 23:51:44 UTC  
> Url: https://github.com/boostorg/parser/issues/41#issuecomment-1882000345  

> In section [Parsing In Detail](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/parsing_in_detail.html), I do not understand this sentence:  
>   
> > Step 1) The input is "1 2 3", and the stack of active parsers is `int_pairs_plus` -> `+int_pair` -> `int_pair` -> `bp::int_`.  
>   
> Is this a mistake? Or is it correct? But what does the arrow represent here? Does this mean a four-element stack?  
  
Add a separate issue for this.
