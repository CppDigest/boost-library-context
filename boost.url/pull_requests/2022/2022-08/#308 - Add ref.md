# #308 Add ref [Merged]

> Username: vinniefalco  
> Created at: 2022-08-02 23:02:46 UTC  
> Updated at: 2022-08-03 04:47:31 UTC  
> Merged at: 2022-08-03 02:19:44 UTC  
> Closed at: 2022-08-03 02:19:44 UTC  
> Url: https://github.com/boostorg/url/pull/308  

This adds `ref` and `squelch`

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-02 23:09:43 UTC  
> Url: https://github.com/boostorg/url/pull/308#issuecomment-1203298835  

An automated preview of the documentation is available at [https://308.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://308.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-08-03 00:40:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/308#pullrequestreview-1059572139  

This new squelch rule is kind of cool.

📁 include/boost/url/grammar/sequence_rule.hpp

```diff
 136 |+ 
 137 |+ template<class Rule>
 138 |+ struct squelch_rule_t
```

> Username: alandefreitas  
> Created_at: 2022-08-03 00:35:18 UTC  
> Updated_at: 2022-08-03 00:40:15 UTC  
> Url: https://github.com/boostorg/url/pull/308#discussion_r936138057  

It's an implementation detail anyway, is this term usual? I've never seen it in my life. I only undertood what it means after reading the code.

---

📁 include/boost/url/grammar/sequence_rule.hpp

```diff
 219 |+ detail::squelch_rule_t<Rule>
 220 |+ #endif
 221 |+ squelch( Rule const& r ) noexcept
```

> Username: alandefreitas  
> Created_at: 2022-08-03 00:38:57 UTC  
> Updated_at: 2022-08-03 00:40:15 UTC  
> Url: https://github.com/boostorg/url/pull/308#discussion_r936139347  

Oh... not a detail. I think I would just call it "ignore_value" or something.  
  
```cpp  
result< std::tuple< pct_encoded_view, string_view > > rv = grammar::parse(  
        "www.example.com:443",  
        grammar::sequence_rule(  
            pct_encoded_rule(unreserved_chars + '-' + '.'),  
            grammar::ignore_value( grammar::char_rule( ':' ) ),  
            grammar::token_rule( grammar::digit_chars ) ) );  
```

> Username: vinniefalco  
> Created_at: 2022-08-03 01:14:32 UTC  
> Url: https://github.com/boostorg/url/pull/308#discussion_r936152021  

I like `squelch` over `ignore_value` since squelch is shorter.

> Username: alandefreitas  
> Created_at: 2022-08-03 01:17:39 UTC  
> Updated_at: 2022-08-03 01:17:54 UTC  
> Url: https://github.com/boostorg/url/pull/308#discussion_r936153295  

`squelch` took me longer because I had to look in the dictionary. :laughing:

> Username: vinniefalco  
> Created_at: 2022-08-03 01:19:49 UTC  
> Url: https://github.com/boostorg/url/pull/308#discussion_r936154167  

Nothing wrong with that in fact, its a bonus because now we have another tool in the toolbox (a new word).


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-08-03 01:15:57 UTC  
> Updated_at: 2022-08-03 01:16:08 UTC  
> Url: https://github.com/boostorg/url/pull/308#issuecomment-1203374061  

Another possibility is we add `delim_rule::operator~()` which applies a transform, so you can write  
```  
   ~delim_rule(':')  
```  
     
and this will be the same as squelch. But I figure that first we should publish something which uses the non-sugared canonical syntax and get that straight first, get some field experience, and then we can see about creating operator shortcuts.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-03 01:24:47 UTC  
> Url: https://github.com/boostorg/url/pull/308#issuecomment-1203378678  

An automated preview of the documentation is available at [https://308.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://308.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
