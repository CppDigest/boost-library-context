# #3 Bugfix/basic expr [Merged]

> Username: vtnerd  
> Created at: 2014-04-14 00:50:52 UTC  
> Updated at: 2014-06-20 08:54:53 UTC  
> Merged at: 2014-04-14 01:52:04 UTC  
> Closed at: 2014-04-14 01:52:04 UTC  
> Url: https://github.com/boostorg/phoenix/pull/3  

Phoenix terminals use proto::basic_expr but operators use proto::expr since phoenix implements the operators directly. This tells proto to use proto:basic_expr when building the syntax tree for consistency.  
  
A little more background information can be found on the mailing list: http://boost.2283326.n4.nabble.com/Phoenix-and-proto-basic-expr-td4661267.html

---

## Comment 1

> Username: vtnerd  
> Created_at: 2014-04-14 00:52:54 UTC  
> Url: https://github.com/boostorg/phoenix/pull/3#issuecomment-40325753  

Forgot to mention, all of the phoenix tests still compile + pass in clang.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-04-14 01:52:09 UTC  
> Url: https://github.com/boostorg/phoenix/pull/3#issuecomment-40327650  

Splendid! Thank you!

---
