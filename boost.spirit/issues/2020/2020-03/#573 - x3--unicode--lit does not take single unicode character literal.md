# #573 - x3::unicode::lit does not take single unicode character literal [Open]

> Username: saki7  
> Created at: 2020-03-05 00:22:19 UTC  
> Updated at: 2025-05-10 00:44:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/573  

These two both work:  
```cpp  
// defined in boost/spirit/home/x3/string/literal_string.hpp  
auto const dollar_def = x3::standard::lit("$"); // double quotes  
  
// `lit` delegates to: boost/spirit/home/x3/char/char.hpp  
auto const dollar_def = x3::standard::lit('$'); // single quotes  
```  
  
For unicode characters, the single quoted one won't work:  
```cpp  
// defined in boost/spirit/home/x3/string/literal_string.hpp  
auto const dollar_def = x3::unicode::lit(U"$"); // double quotes  
  
// fails to compile  
auto const dollar_def = x3::unicode::lit(U'$'); // single quotes  
```  
  
It seems like the single char `lit` interfaces for unicode chars are missing in char.hpp.  
  
I'm currently using `U"double quotes"` for single letter grammars, which is obviously against the optimization.

---

## Comment 1

> Username: saki7  
> Created at: 2020-03-05 01:10:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/573#issuecomment-594974958  

Dear maintainers: I'd like to PR if extending char.hpp is the correct approach. Please give me the green light.

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-03-06 01:43:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/573#issuecomment-595554726  

Just open a PR if you have a fix. Do not forget about tests.

---

## Comment 3

> Username: saki7  
> Created at: 2025-05-10 00:44:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/573#issuecomment-2868155732  

I have a private patch on my repo that's been working for years. I've actually become a maintainer of this library, so I guess I need to polish my implementation and submit a PR.  
  
@saki7 Why you didn't submit a PR 5 years ago? It could have made people's life (including mine) a lot easier.
