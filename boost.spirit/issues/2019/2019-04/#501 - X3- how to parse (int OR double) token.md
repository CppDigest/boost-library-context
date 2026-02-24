# #501 - X3: how to parse (int OR double) token? [Closed]

> Username: Xeverous  
> Created at: 2019-04-30 15:14:07 UTC  
> Updated at: 2019-04-30 15:46:39 UTC  
> Closed at: 2019-04-30 15:46:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/501  

suppose that some AST is `variant<int, double>`  
  
problem:  
  
- grammar `x3::int_ | x3::double_` is not ok because `3.5` is parsed as `int` then `.` is a parse error  
- grammar `x3::double_ | x3::int_` is not ok either because `3` is parsed as `double` instead of `int`  
  
Is there any safe way to look-ahead in the first case? Also, `x3::double_` also supports E notation so I'm worried if there is more complexity behind it.  
  
Basically I want to parse numbers to the simplest type possible while not triggering parse errors on `.5` or `e+1`

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-04-30 15:46:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/501#issuecomment-488005432  

Your grammar is ambiguous. You literally said it here:  
  
> `3` is parsed as `double` instead of `int`  
  
Use the `strict_real_policies` real policy to disambiguate it.  
  
This question rises from time to time. You should have googled for it first.
