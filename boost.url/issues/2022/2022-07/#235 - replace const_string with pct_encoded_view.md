# #235 - replace const_string with pct_encoded_view [Closed]

> Username: alandefreitas  
> Created at: 2022-07-19 01:54:44 UTC  
> Updated at: 2022-07-26 20:47:12 UTC  
> Closed at: 2022-07-26 20:47:12 UTC  
> Url: https://github.com/boostorg/url/issues/235  

[Steps](https://cpplang.slack.com/archives/C01JR6C9C4U/p1657946767267519):  
  
- Root out const_string, ✅  
- ~~Cut half of the segments/params containers out~~,   
- ~~Cut out half of the observers~~,   
- Change the `pct_encoded_rule` to use the view, and ✅  
- ~~Change the pct_encoding API per the issue~~ (fixed in https://github.com/CPPAlliance/url/pull/234)  
- Adapt url and url member functions  
    - add `set_XXXXX(pct_encoded_view)` ✅  
    - `XXXXX()` can become `noexcept`: we need a private constructor that lets us bypass validation ✅  
- Review javadocs ✅  
- Review docs ✅  
- Check coverage ✅

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-19 01:55:20 UTC  
> Url: https://github.com/boostorg/url/issues/235#issuecomment-1188509061  

Are we also going to cut out half the setters?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-19 12:16:12 UTC  
> Url: https://github.com/boostorg/url/issues/235#issuecomment-1188979392  

> Are we also going to cut out half the setters?  
  
Think about that question carefully

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-19 23:29:24 UTC  
> Url: https://github.com/boostorg/url/issues/235#issuecomment-1189647308  

or you can do something like this  
  
```  
namespace detail {  
  
struct unchecked_tag {};  
  
struct encoded_view  
{  
  static  
  pct_encode_view  
  construct(  
    string_view s,  
    pct_decode_opts const& opt) const noexcept;  
};  
  
} // detail  
  
class pct_encoded_view  
{  
  friend detail::encoded_view;  
  pct_encoded_view(  
    detail::unchecked_tag,  
    string_view s,  
    pct_decode_opts const& opt) noexcept;  
  ...  
```  
  
to give access to the private constructor  
  
you friend the one detail class, and then everyone in the lib can call it  
New  
[8:28](https://cpplang.slack.com/archives/C01JR6C9C4U/p1658273291188079)  
otherwise you will need many friends
