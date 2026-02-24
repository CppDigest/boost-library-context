# #106 X3: Add support for custom character filters on the symbols parser [Closed]

> Username: teajay-fr  
> Created at: 2015-05-25 21:28:40 UTC  
> Updated at: 2025-05-09 15:13:51 UTC  
> Closed at: 2025-05-09 15:10:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/106  

Just added an additional template parameter to the symbols parser as in qi, except it's not the filter directly, only a helper struct to get hold of the correct filter. This was necessary to continue to support case insensitive parsing.

---

## Review 1 [Commented]

> Username: saki7  
> Created_at: 2025-05-09 15:02:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/106#pullrequestreview-2828683303  

📁 include/boost/spirit/home/x3/string/symbols.hpp

```diff
  46 |+         }
  47 |+     };
  48 |+ 
```

> Username: saki7  
> Created_at: 2025-05-09 15:02:04 UTC  
> Updated_at: 2025-05-09 15:09:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/106#discussion_r2081872291  

I'm afraid that this approach will lead to ODR violation. The code itself seems okay, but we must think of more complex situations.  
  
A user would expect that they're the only person who is specializing `get_default_symbol_filter` for their arbitrary `Encoding`, but in reality, if any conflicting definitions (even on 3rd party libraries) exist in different translation units, it will trigger UB.  
  
That's very likely to happen, since the predefined encodings such as `char_encoding::unicode` would normally be passed to the `symbols_parser`, in contrast to the custom type `braille::encoding::dots` used in your example.  
  
Proposed class must be at least not using the word `default`, or we should just make it internal. But, even so, we definitely need a robust customization point which correctly handles (or friendly forbids the wrong usage at compile-time) in case of potentially conflicting definitions.  
  
It shall be implemented in a more C++20-ish way, such as explicitly requiring a CPO with specific signatures that is defined per each parser primitives.  
  
That said, I don't think (and I can't demand to) implement that kind of huge change on top of this PR.

> Username: saki7  
> Created_at: 2025-05-09 15:13:51 UTC  
> Updated_at: 2025-05-09 15:13:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/106#discussion_r2081903937  

Well, a similar feature seems to be implemented already in 2025. But that's a different story, I guess.  
Apologies that we couldn't review your PR in 2015.


---
