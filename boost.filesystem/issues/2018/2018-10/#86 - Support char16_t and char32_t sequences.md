# #86 - Support char16_t and char32_t sequences [Open]

> Username: igoloe  
> Created at: 2018-10-03 12:14:43 UTC  
> Updated at: 2020-09-01 08:26:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/86  

Contrary to what is stated in the docs, source code does no seem to have support for utf16 an utf32 char sequences. At least specialization of path_traits for is_pathable is missing and I assume for convert as well. I am not sure, what the internal decisions are, maybe its just postponed?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-03 18:21:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/86#issuecomment-623156866  

Boost.Filesystem does support UTF-16 and UTF-32 through `wchar_t`. `charN_t` are the newer additions to C++ which are not supported yet.

---

## Comment 2

> Username: mbs-c  
> Created at: 2020-09-01 08:26:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/86#issuecomment-684563909  

Could the documentation be updated to reflect that? Right now, it contains references to `char16_t`, `char32_t`, `u16string` and `u32string` in various places:  
  
- https://www.boost.org/doc/libs/1_74_0/libs/filesystem/doc/reference.html#Introduction  
- https://www.boost.org/doc/libs/1_74_0/libs/filesystem/doc/reference.html#class-path  
- https://www.boost.org/doc/libs/1_74_0/libs/filesystem/doc/reference.html#path-Requirements
