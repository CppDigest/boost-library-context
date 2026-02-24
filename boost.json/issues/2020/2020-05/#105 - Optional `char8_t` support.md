# #105 - Optional `char8_t` support [Closed]

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 23:50:16 UTC  
> Updated at: 2020-09-26 03:00:06 UTC  
> Closed at: 2020-09-26 03:00:06 UTC  
> Url: https://github.com/boostorg/json/issues/105  

Just an idea, I don't think it is necessary at the moment. Probably can perform better because of different aliasing rules. The problem can be that all the parsing things typically work only with `char`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-31 02:36:05 UTC  
> Updated at: 2020-05-31 02:36:13 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-636411775  

This is a C++11 library. And I don't see how `char8_t` can possibly improve performance. Perhaps you can show me on https://godbolt.org?

---

## Comment 2

> Username: Eelis  
> Created at: 2020-08-25 19:16:10 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-680219970  

https://godbolt.org/z/1bqzbY is an example of how `char8_t` can improve performance (notice how `slow` does more memory accesses than `fast`, because unlike `fast`, `slow` can't be sure that `*p = '3';` doesn't modify `i`).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-05 02:07:00 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687521860  

This might be good for serialization, but when parsing we use `char const*` not `char*`. And we don't write to the input.

---

## Comment 4

> Username: Eelis  
> Created at: 2020-09-05 02:31:26 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687529388  

Writing to the input is not essential; here's a modified example where the input is not written to, but `char8_t` still gives an advantage: https://godbolt.org/z/njKj3s

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-05 02:37:56 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687531358  

so what we can do is make a private alias in `basic_parser`:  
```  
#ifdef __cpp_has_char8  
using char_type = char8_t;  
#else  
using char_type = char;  
#endif  
```  
  
And then change `char const*` to `char_type const*` internally, doing a big `reinterpret_cast` on entry, and get all these benefits?

---

## Comment 6

> Username: Eelis  
> Created at: 2020-09-05 02:40:38 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687532128  

I don't know, I haven't used `char8_t` for real yet, only played with it in godbolt snippets. :)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-09-05 02:47:05 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687534183  

can't hurt to try

---

## Comment 8

> Username: sdkrystian  
> Created at: 2020-09-05 03:00:24 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687536603  

By doing a reinterpret_cast we alias the input as `char8_t`... and end up with UB

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-09-05 03:03:10 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687537016  

Is it really UB though? Or is it only UB in papers that float through WG21? Can you show UB for this using an existing compiler?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-09-05 13:50:37 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-687614279  

Yes it is technically UB, so we can't embrace this just yet. I'd like to see what the performance is like after refactoring to use a nested `char_type` set to `char8_t`. Then we can revisit the topic. After 1.75 release though.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2020-09-26 03:00:06 UTC  
> Url: https://github.com/boostorg/json/issues/105#issuecomment-699284730  

I don't see this as practical
