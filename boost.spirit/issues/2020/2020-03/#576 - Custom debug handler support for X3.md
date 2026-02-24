# #576 - Custom debug handler support for X3 [Open]

> Username: saki7  
> Created at: 2020-03-05 07:25:16 UTC  
> Updated at: 2025-05-10 00:41:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/576  

In Spirit.Qi, we had `qi::debug(...)` function for specifying custom debug handler.   
https://github.com/boostorg/spirit/blob/889babc3839e12ac0bba448ecd7150956d17c783/include/boost/spirit/home/qi/nonterminal/debug_handler.hpp#L75-L123  
  
However in X3's current implementation, `simple_trace` is hard-coded:  
  
https://github.com/boostorg/spirit/blob/889babc3839e12ac0bba448ecd7150956d17c783/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L60-L91  
  
I'd love to see custom debug handler support in X3.  
  
__EDIT:__ The reason why I need this functionality is because X3's default debug handler prints Unicode strings as an array of integer code points. What I expected to see was e.g. `['H', 'e', 'l', 'l', 'o']`, but instead I get `[72, 101, 108, 108, 111]`. Perhaps this specific issue is more suited to be handled in a separate issue...

---

## Comment 1

> Username: theodelrieu  
> Created at: 2020-12-30 00:15:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/576#issuecomment-752283224  

I have the same need, it's very hard to debug what's going on with all those numbers

---

## Comment 2

> Username: djowel  
> Created at: 2020-12-30 00:29:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/576#issuecomment-752285736  

Would anyone care to submit a PR?

---

## Comment 3

> Username: theodelrieu  
> Created at: 2020-12-30 00:41:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/576#issuecomment-752287740  

Sure, I wonder if one should be able to fully customize the handler, or if it's sufficient to add a customization point for `token_printer_debug` (in `support/traits/print_token.hpp`).

---

## Comment 4

> Username: djowel  
> Created at: 2020-12-30 07:17:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/576#issuecomment-752356507  

I'd personally prefer customization points. Start with something usable but simple, then we can improve the API as needed.

---

## Comment 5

> Username: theodelrieu  
> Created at: 2020-12-30 19:24:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/576#issuecomment-752731348  

I'll tackle this next week, I have to write a patch to make `char32_t` the `char_type` of the `unicode` encoding, which can have an impact on how to solve this unicode debug handler problem.
