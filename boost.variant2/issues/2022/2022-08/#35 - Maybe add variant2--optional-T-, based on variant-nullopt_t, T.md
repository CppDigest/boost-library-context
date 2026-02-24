# #35 - Maybe add variant2::optional<T>, based on variant<nullopt_t, T> [Open]

> Username: pdimov  
> Created at: 2022-08-03 07:52:23 UTC  
> Updated at: 2022-10-05 12:24:24 UTC  
> Url: https://github.com/boostorg/variant2/issues/35  

w/ JSON support etc

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-01 17:25:06 UTC  
> Url: https://github.com/boostorg/variant2/issues/35#issuecomment-1264429891  

Or maybe just add to `variant<void, T>` the optional-like accessors `has_value` and `operator*`.

---

## Comment 2

> Username: glenfe  
> Created at: 2022-10-05 12:24:24 UTC  
> Url: https://github.com/boostorg/variant2/issues/35#issuecomment-1268366747  

I'd vote for `variant2::optional<T>`
