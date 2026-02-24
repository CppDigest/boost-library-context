# #160 - Consider adding omit, prefix and suffix template parameters to all parsers [Open]

> Username: tzlaine  
> Created at: 2024-03-08 00:58:44 UTC  
> Updated at: 2024-03-08 00:58:44 UTC  
> Url: https://github.com/boostorg/parser/issues/160  

The new parameters would be something like:  
  
```c++  
bool Omit, typename Prefix, typename Suffix, ...  
```  
  
It turns out that the `omit_parser::call()` shows up pretty high in profiling of Parser.  If each parser knew if it was wrapped in a notional `omit[]`, `omit[]` could just take a parser, and return a copy with `true` for the template parameter `Omit`.  This means that the actual function call to `omit_parser::call()` (and `omit_parser` itself) goes away.  
  
No so sure the other two would be as big a win, but a lot of parsers are something like `eps(cond) >> p` (that's what `if_(cond)` expands to).  If all those instances, and `expect_parser` itself, were just built in to every parser (with a `nope` when there's no prefix and/or sufffix condition(s)), that might similarly speed things up.  `expect_parser` would just go away in that case.
