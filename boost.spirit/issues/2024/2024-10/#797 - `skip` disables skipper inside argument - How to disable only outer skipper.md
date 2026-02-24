# #797 - `skip` disables skipper inside argument - How to disable only outer skipper [Closed]

> Username: Flamefire  
> Created at: 2024-10-12 10:35:39 UTC  
> Updated at: 2025-09-04 06:24:35 UTC  
> Closed at: 2025-09-03 23:47:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/797  

`skip[a]` is documented as "Re-establish the skipper that got inhibited by lexeme or no_skip. "  
  
It is underspecified if that applies to the outer and/or inner skipper.  
  
Take this example: `x3::lexeme[XXX >> x3::skip[YYY] ];`  
  
Is `skip` supposed to affect `YYY`, `XXX >> YYY` or both if XXX and YYY can contain skippers too?  
  
How would the parser look like to join 2 parsers that might allow spaces inside without any space?  
  
Example from Slack: https://godbolt.org/z/GYYejjrEd  
  
```  
auto const identifier = x3::lexeme[+x3::alpha];  
auto const expr = identifier >> "=" >> identifier;   
  
auto const grammar = x3::lexeme[identifier >> '.' >> x3::skip[expr] ];  
```

---

## Comment 1

> Username: saki7  
> Created at: 2025-09-03 23:47:05 UTC  
> Updated at: 2025-09-04 01:36:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/797#issuecomment-3251147032  

At first (on May) I thought we would need an addition to the documentation to clarify this behavior. However, I think it's not necessary now.  
  
Quoting your code:  
  
```cpp  
x3::lexeme[XXX >> x3::skip[YYY]];  
```  
  
the explanation:  
  
> Re-establish the skipper that got inhibited by lexeme or no_skip  
  
is pretty much trivial.   
  
Step-by-step interpretation:  
  
- The only operand that is wrapped inside `x3::skip` is `YYY`. So it is clear that the subject of re-establishment is `YYY`.   
- The only functionality that appears to somehow inhibit the skipper is `x3::lexeme`.  
- The operand for `x3::lexeme` is the compound sequence, i.e. `XXX >> x3::skip[YYY]`.  
- So the entire snippet can be interpreted as "lexeme inhibits the skipper for the entire compound parser. But then the skipper is explicitly re-established for YYY."  
- If above interpretation is false, what "inhibited skipper" would you expect to be re-established for `YYY`?   
  
There's no ambiguity in both syntax and semantics.   
  
Parser combinator library is generally a very difficult library to understand, and requires minimum practical experimentation on user's part. Even if a first-time user of Spirit does not understand the general notation of Spirit, one can soon realize how the operands are processed just by experimenting with their program for few hours.
