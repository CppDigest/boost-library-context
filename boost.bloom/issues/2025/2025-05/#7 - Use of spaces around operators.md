# #7 - [Codestyle] Use of spaces around operators [Open]

> Username: Flamefire  
> Created at: 2025-05-22 11:28:50 UTC  
> Updated at: 2025-07-01 13:07:45 UTC  
> Url: https://github.com/boostorg/bloom/issues/7  

I know codestyle is highly subjective, but I'd like to make a suggestion:  
  
Would you mind adopting a codestyle with spaces around operators?  
Without those it looks like a token soup that is hard to parse visually for me.  
  
E.g.:  
- `for(std::size_t i=0;i<k/8;++i){`  
- `x?1+constexpr_bit_width(x>>1):0;`  
- `x|=Block(1)<<(h&mask);`  
  
could be:  
- `for(std::size_t i = 0; i < k / 8; ++i){`  
- `x ? 1 + constexpr_bit_width(x >> 1) : 0;`  
- `x |= Block(1) << (h&mask);`  
  
Or at least for "delimiters"  
- `for(std::size_t i=0; i<k/8; ++i){`  
- `x ? 1+constexpr_bit_width(x>>1) : 0;`  
- `x |= Block(1)<<(h&mask);`  
  
To me this makes it much easier to see which parts belong together and not miss e.g. the "?", "|" or ";"

---

## Comment 1

> Username: godexsoft  
> Created at: 2025-07-01 13:07:45 UTC  
> Url: https://github.com/boostorg/bloom/issues/7#issuecomment-3023950675  

Great library but could use `clang-format` as part of CI 👍
