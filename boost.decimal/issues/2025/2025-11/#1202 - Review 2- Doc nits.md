# #1202 - Review 2: Doc nits [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:55:40 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-07 13:59:27 UTC  
> Url: https://github.com/boostorg/decimal/issues/1202  

- [x] In the section titled "Fundamental Operations", the phrase "The fundamental operations of numerical type (e.g. >, ==, +, etc.) are overloaded." should actually say "numerical types".  
- [x] In Examples -> Construction -> Literals and Constants, the statement “numeric_limits is overloaded for all decimal types” should say “specialized” instead of "overloaded".  
- [x] The sentence "This example shows how to parse historical stock data from file and use it" would read more naturally as "from a file".  
- [x] The navigation panel contains a typo: "Formating support" should be "Formatting support".  
- [x] Title case in the navigation panel is used inconsistently (see the entries ending with "support".)  
- [x] On the six pages dedicated to the six decimal types, there are notes saying "This support has been removed in v6.0.0". I think they would be better as "This name has been removed". You might omit the notes altogether if the library is accepted into Boost.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-05 10:09:56 UTC  
> Url: https://github.com/boostorg/decimal/issues/1202#issuecomment-3490321009  

This all seems reasonable to me. The versioned removals was mostly to the benefit of current customers, which will of course completely change if accepted.
