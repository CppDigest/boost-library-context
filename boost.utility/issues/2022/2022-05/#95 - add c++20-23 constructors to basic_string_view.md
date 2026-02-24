# #95 - add c++20/23 constructors to basic_string_view [Closed]

> Username: sehe  
> Created at: 2022-05-02 05:43:38 UTC  
> Updated at: 2023-04-20 13:04:33 UTC  
> Closed at: 2022-05-02 22:24:33 UTC  
> Url: https://github.com/boostorg/utility/issues/95  

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

> Username: Lastique  
> Created at: 2022-05-02 22:16:51 UTC  
> Url: https://github.com/boostorg/utility/issues/95#issuecomment-1115424488  

Given the feedback in https://github.com/boostorg/core/issues/113, I kind of agree that the second constructor is probably a bad idea. The first one requires a modern standard library as it must only compile for contiguous iterators, which is C++20 I think. We currently don't have the infrastructure for this. I don't find the third one very useful, but fine, I guess.

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-02 22:24:32 UTC  
> Url: https://github.com/boostorg/utility/issues/95#issuecomment-1115429818  

I agree on the third one.  
  
Let's accept this as not desirable for now. In reality, the purpose of string_view alternatives outside the standard library are either supporting older compilers (so the missing c++20+ constructors are moot) or adding interoperability benefits (e.g. `core::string_view`).

---

## Comment 3

> Username: GielVanSchijndel-TomTom  
> Created at: 2023-04-20 12:39:36 UTC  
> Updated at: 2023-04-20 13:04:33 UTC  
> Url: https://github.com/boostorg/utility/issues/95#issuecomment-1516257153  

What about adding a more restricted version of the first constructor?  
```cpp  
explicit constexpr basic_string_view(const_iterator first, const_iterator last);  
```  
  
This would at least provide compatibility with `iterator_range`'s [`copy_range`](https://github.com/boostorg/range/blob/f1215fbda2d13d10913983611dbbd4ec4421648a/include/boost/range/iterator_range_core.hpp#L831-L843) and as a result make `string_view` usable with string algorithms *without* needing to do a follow up conversion. Take for example this reduced example taken from code we're actually using (where we're forced to have an intermediate `std::vector<boost::iterator_range<const char*>>` to convert from):  
```cpp  
auto do_split(boost::string_view str) {  
    std::vector<boost::string_view> tokens;  
    boost::split(tokens, str, [](const char c) { return c == ','; });  
    return tokens;  
}  
```  
  
This would seemingly provide partial forward compatibility with C++20's constructor without creating backwards compatibility problems in the future (that I can see).  
  
Edit: I added this as a comment on https://github.com/boostorg/core/issues/113#issuecomment-1516271061. But I was unaware that's a different `string_view` than this one.
