# #73 - string_ref::remove_prefix() doesn't conform with reference [Closed]

> Username: classix-do  
> Created at: 2021-03-01 12:37:48 UTC  
> Updated at: 2021-03-01 16:02:38 UTC  
> Closed at: 2021-03-01 16:02:38 UTC  
> Url: https://github.com/boostorg/utility/issues/73  

According to the [implementation reference](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3442.html#strings.string_ref.remove_prefix) for string_ref,  `remove_prefix(n)` should have the effect of:  
```!cpp  
*this = substr(n, npos);  
```  
For `constexpr basic_string_ref substr(size_type pos, size_type len=npos) const;` the reference reads:  
 > Throws: `out_of_range` if `pos > size()`.  
  
  
So  
```!cpp  
boost::string_ref().remove_prefix(1);   // should throw, but it doesn't  
boost::string_ref().remove_suffix(1);   // behaves correct (does not throw)  
boost::string_ref().substr(1);          // behaves correct (does throw)  
```  
  
Is there some rationale behind this non standard implementation? I couldn't find any in the docs. It sure is more convenient to not have a throwing `remove_prefix()` method. But why were only the semantics of `remove_prefix()` changed and not of `substr()` itself?

---

## Comment 1

> Username: mclow  
> Created at: 2021-03-01 14:20:05 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-787984404  

Probably because it pre-dates the standard. I'll take a look

---

## Comment 2

> Username: mclow  
> Created at: 2021-03-01 14:53:31 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-788009646  

Addressed in https://github.com/boostorg/utility/commit/601fc9371ff157b05bc16622871e92fb0f94d272

---

## Comment 3

> Username: classix-do  
> Created at: 2021-03-01 15:41:37 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-788045344  

Wow, that was quick.   
  
Is the link in the header (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3442.html) actually the valid implementation reference for this class? If so then now `remove_suffix()` is not behaving according to the [reference](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3442.html#strings.string_ref.remove_suffix), because `remove_suffix(n)` should behave like:  
```!cpp  
*this = substr(0, size() - n);  
```  
If `n > size()` then the resulting length will be a very large number and `substr(pos, len)` doesn't throw if `len > size()` because it also allows `len` to be `string_ref::npos`.  
  
> Effects:  
Determines the effective length `rlen` of the string to copy as the smaller of `len` and `size() - pos`.  
Returns:  
`basic_string_ref(data()+pos, rlen).`  
  
If the implementation reference for string_ref is `std::string_view` then the current behavior and the old behavior are both correct, because passing `n > size()` to `remove_prefix()` or `remove_suffix()` are both simply undefined behavior.  
  
I asked about whether the initial implementation was intentional, because strictly sticking to the linked reference leads to this asymmetrical (and in my opinion ugly) behavior of `remove_prefix()` and `remove_suffix()`. Including a new exception could potentially break existing code, which relies on the old behavior.   
  
I would argue that either `substr()`should throw in both cases:  
 * `pos > size()`  
 *  `len > size()`  (this would however prevent `string_ref::npos` to be used as default argument)  
or in neither of these cases and implement it like this:  
  
```!cpp  
basic_string_ref substr(size_type pos, size_type n=npos) const {  
    pos = (std::min)(pos, size());  
    return basic_string_ref(data() + pos, (std::min)(size() - pos, n));  
}  
```  
This way the behavior of `remove_prefix()`, `remove_suffix()` and `substr()` match up with the exception that `substr()` won't throw an exception if `pos > size()`.  
  
If `boost::string_ref` is supposed to behave as close to `std::string_view` as possible, then the old behavior would be best. Sorry for the confusion.

---

## Comment 4

> Username: mclow  
> Created at: 2021-03-01 15:52:37 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-788056991  

Bad me. I took your word for it, instead of actually reading the standard.    
`remove_prefix` and `remove_suffix` both have a precondition that is `n <= size()`  
  
I agree that it is UB, so depending on consistency between string_ref and string is a non-starter.  
If it's UB, then it needn't be consistent with anything - even itself on subsequent calls.

---

## Comment 5

> Username: mclow  
> Created at: 2021-03-01 15:53:03 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-788057291  

I'll be reverting my change.

---

## Comment 6

> Username: classix-do  
> Created at: 2021-03-01 16:02:38 UTC  
> Url: https://github.com/boostorg/utility/issues/73#issuecomment-788064904  

Sorry for the confusion 😥
