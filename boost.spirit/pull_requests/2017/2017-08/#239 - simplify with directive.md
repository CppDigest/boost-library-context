# #239 simplify with directive [Merged]

> Username: wanghan02  
> Created at: 2017-08-21 11:54:57 UTC  
> Updated at: 2020-04-15 09:53:26 UTC  
> Merged at: 2017-08-22 19:03:53 UTC  
> Closed at: 2017-08-22 19:03:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/239  

1. when used as x3::with<ID>(x), where x is an lvalue, we inject an lvalue reference of x to the context.  
2. when used as x3::with<ID>(x), where x is a const lvalue, we inject a const lvalue reference of x to the context.  
3. when used as x3::with<ID>(x), where x is an rvalue, we move the rvalue to the member of with_directive and inject an lvalue reference of that member to the context.  
4. No copy is applied; std::ref can still be used but not needed.  
5. remove unused with_context

---

## Comment 1

> Username: djowel  
> Created_at: 2017-08-21 23:59:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/239#issuecomment-323882243  

Looks good! Can you provide some test files?

---

## Comment 2

> Username: wanghan02  
> Created_at: 2017-08-22 14:16:11 UTC  
> Updated_at: 2017-08-22 14:40:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/239#issuecomment-324040082  

@djowel Test cases have been added to test/x3/with.cpp  
**rvalue injection**: The 1st commit actually inject a const ref to the member variable because parse(...) is a const method. This behaves the same as before (but without copying), but does not really make sense. I think the best way to use with directive is:  
  
- Inject non-const lvalue: able to modify during parsing, the original lvalue is modified.   
- Inject rvalue: able to modify during parsing.   
- Inject const lvalue: not able to modify during parsing.   
  
So I brought back the with_value_holder and change the T const specialization to T& specialization, and makes rvalue injection modifiable. Test cases have been changed to adapt to the new requirement.

---

## Comment 3

> Username: djowel  
> Created_at: 2017-08-22 19:03:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/239#issuecomment-324121478  

wonderful!

---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2020-03-31 14:12:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/239#pullrequestreview-384774607  

📁 include/boost/spirit/home/x3/directive/with.hpp

```diff
  21 |+     {
  22 |+         typedef unary_parser<Subject, Derived> base_type;
  23 |+         mutable T val;
```

> Username: Kojoley  
> Created_at: 2020-03-31 14:12:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/239#discussion_r400946306  

Could you explain why is it `mutable`?  
  
1. The tests you had added do not cover it.  
2. Allowing to mutate the value creates thread-safety risks.  
3. It does not play well with `constexpr`, especially with Clang 6 and below.

> Username: wanghan02  
> Created_at: 2020-04-15 09:53:25 UTC  
> Updated_at: 2020-04-15 09:53:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/239#discussion_r408720785  

The idea was rvalue injection should be mutable in const parser as well. Given parsers are immutable in the parse functions, rvalue injection would be immutable without `mutable` keyword.   
  
But I also agree with you on the issues it brings.  I don't have a solution at the moment.


---
