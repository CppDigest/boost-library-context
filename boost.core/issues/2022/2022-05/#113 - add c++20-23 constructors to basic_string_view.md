# #113 - add c++20/23 constructors to basic_string_view [Closed]

> Username: sehe  
> Created at: 2022-05-02 05:45:46 UTC  
> Updated at: 2023-04-20 13:05:05 UTC  
> Closed at: 2022-09-18 22:15:40 UTC  
> Url: https://github.com/boostorg/core/issues/113  

The following constructors were added:  
  
```c++  
template <class It, class End> constexpr basic_string_view(It first, End last);  
template <class R> constexpr basic_string_view(R&& r);  
constexpr basic_string_view(std::nullptr_t) = delete;  
```  
  
(where `It`/`End` and `R` are subject to the usual restrictions)  
  
People might expect these to be present on the Boost implementation.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-05-02 12:23:44 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1114786931  

See http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2516r0.html.

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-02 12:52:15 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1114812744  

Without clicking the link (on my phone) I assume this is this about the problems with these constructors?  
  
If so is what does your comment mean? Can you clarify whether support for these features is rejected or postponed.  
  
If rejected feel free to close!

---

## Comment 3

> Username: pdimov  
> Created at: 2022-05-02 12:59:06 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1114818061  

I'm not planning to add the relaxed range or iterator constructors unless there is a specific practical need arising from actual user code. Their absence is deliberate. string_view has too many implicit conversions as it is.  
  
The nullptr_t one is an entirely separate topic. Probably should be added.

---

## Comment 4

> Username: sehe  
> Created at: 2022-05-02 15:39:38 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1115046925  

Thanks for the clarifications. I for one don't think the `nullptr` overload saves lives (beyond being more standards-similar). I'll close this issue

---

## Comment 5

> Username: sehe  
> Created at: 2022-05-02 15:44:17 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1115051616  

(Keeping around for the `nullptr` constructor.)

---

## Comment 6

> Username: Lastique  
> Created at: 2022-09-18 22:42:14 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1250401249  

FYI, C++23 has made the range constructor `explicit` in [P2499](https://wg21.link/p2499). It's not as harmful this way.

---

## Comment 7

> Username: muggenhor  
> Created at: 2023-04-20 12:49:54 UTC  
> Updated at: 2023-04-20 12:52:02 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1516271061  

What about adding a more restricted version of the first constructor?  
```cpp  
explicit constexpr basic_string_view(const_iterator first, const_iterator last);  
```  
  
This would seemingly provide partial forward compatibility with C++20's constructor without creating backwards compatibility problems in the future (that I can see).  
  
> I'm not planning to add the relaxed range or iterator constructors unless there is a specific practical need arising from actual user code. Their absence is deliberate. string_view has too many implicit conversions as it is.  
  
As for a use case from actual user code. I'm facing one right now. A reduced example of what we're trying to do:  
```cpp  
auto do_split(boost::string_view str) {  
    std::vector<boost::string_view> tokens;  
    boost::split(tokens, str, [](const char c) { return c == ','; });  
    return tokens;  
}  
```  
  
Unfortunately `split` relies on a range constructor via `iterator_range`'s [`copy_range`](https://github.com/boostorg/range/blob/f1215fbda2d13d10913983611dbbd4ec4421648a/include/boost/range/iterator_range_core.hpp#L831-L843). Adding an `explicit` range constructor that takes `const_iterator` would be sufficient to make that example functional.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-04-20 12:57:24 UTC  
> Updated at: 2023-04-20 12:57:37 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1516281492  

This issue is about `boost::core::string_view` (which already has such a constructor), not about `boost::string_view`.

---

## Comment 9

> Username: muggenhor  
> Created at: 2023-04-20 13:00:01 UTC  
> Updated at: 2023-04-20 13:05:05 UTC  
> Url: https://github.com/boostorg/core/issues/113#issuecomment-1516285653  

Ah, I was unaware there were multiple copies of `string_view`. I'll switch to [commenting on the `utility` version of it](https://github.com/boostorg/utility/issues/95#issuecomment-1516257153).
