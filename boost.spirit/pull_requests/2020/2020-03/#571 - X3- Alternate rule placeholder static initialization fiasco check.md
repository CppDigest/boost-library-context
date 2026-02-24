# #571 X3: Alternate rule placeholder static initialization fiasco check [Merged]

> Username: Kojoley  
> Created at: 2020-03-02 17:58:12 UTC  
> Updated at: 2020-03-03 11:40:06 UTC  
> Merged at: 2020-03-03 11:40:05 UTC  
> Closed at: 2020-03-03 11:40:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/571  

The previous check was done in a very intrusive way, it also was not catching  
the issue when a parser is not inherited from `unary_parser`/`binary_parser`.

---

## Review 1 [Approved]

> Username: djowel  
> Created_at: 2020-03-03 03:40:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/571#pullrequestreview-367684675  

Looks good to me. Feel free to merge.

---

## Comment 2

> Username: wanghan02  
> Created_at: 2020-03-03 08:00:58 UTC  
> Updated_at: 2020-03-03 09:24:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/571#issuecomment-593818043  

@Kojoley This solution is great! `constexpr` rules does not trigger SIOF and normal static rules can trigger it at run time. Thank you!

---
