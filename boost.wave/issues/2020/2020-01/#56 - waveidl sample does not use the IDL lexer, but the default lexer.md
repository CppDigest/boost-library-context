# #56 - waveidl sample does not use the IDL lexer, but the default lexer [Closed]

> Username: jefftrull  
> Created at: 2020-01-12 05:50:23 UTC  
> Updated at: 2020-02-12 14:59:46 UTC  
> Closed at: 2020-02-12 14:59:46 UTC  
> Url: https://github.com/boostorg/wave/issues/56  

This is a port of [TRAC 7822](https://svn.boost.org/trac10/ticket/7822). The original text begins:  
> Dear Boost developpers,  
>  
> The waveidl sample from the wave lib does not use the included IDL lexer. Instead it uses the default cpp lexer. This can be easily tested by setting a breakpoint in the scan function in idl_re.cpp. The reason is that it is explicitly set up like this in the lex_iterator constructor.

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-01-12 05:57:23 UTC  
> Url: https://github.com/boostorg/wave/issues/56#issuecomment-573384956  

I was able to confirm this by building `waveidl` with debug symbols and setting a breakpoint on the `scan()` function inside `idl_re.hpp`. In an unmodified build this function never gets called. However, if you change [the lexer interface generator](https://github.com/boostorg/wave/blob/develop/samples/waveidl/idllexer/idl_lex_iterator.hpp#L176) to use `idllexer` instead of `cpplexer`, it _does_ get called, as the submitter @MSoegtropIMC claimed.
