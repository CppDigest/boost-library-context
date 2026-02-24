# #124 - Docs: improve section "from_chars overview" [Closed]

> Username: akrzemi1  
> Created at: 2024-01-19 04:33:39 UTC  
> Updated at: 2024-01-22 07:16:40 UTC  
> Closed at: 2024-01-22 07:16:40 UTC  
> Url: https://github.com/boostorg/charconv/issues/124  

Section https://master.charconv.cpp.al/#build_from_chars is uncomfortable to read for a couple of resons.  
  
The description of `from_chars_result` is wrong. `ptr` is just a pointer. It doesn't point to the first invalid character, or to `last`: there is no `last` in the scope of `from_chars_result`. `ptr` points to whatever the user wants it to point to. Now that you have defined this class, users can create its objects for whatever purpose they want.  It is only funcitons `from_chars` that choose to use it in a specific way.  
  
Please use computer font for referring to variable names. Sentene like "points to the first character not matching the pattern, or has the value of last if all characters are successfully parsed" is confusing. Words "first" and "last" seem like a pair of similar things, but in your sentence only one of them represents an object (which is not in scope).  
  
Same for `ec`: the user can assign any value they want. "Valid values for are" belongs to the description of `from_chars` funcitons.  
  
The description of comparisons brings no value, and is imprecise. Why not just declare:  
  
```c++  
friend constexpr bool operator==(const from_chars_result& lhs, const from_chars_result& rhs) noexcept = default;  
```  
  
even though it is a c++11 library?  
  
If you insist on having the two declarations, do change their description to:  
  
 * `operator==` - returns `lhs.ptr == phs.ptr && lhs.ec == rhs.ec`,  
 * `operator!` - returns `!(lhs == rhs)`.  
  
I think that the initial section would be better off with some verbal description rather than class/function synopses. It should start from explaining that the convention of this library is to report failures via class `from_chars_result` with these two fields that are *intended` to represent the following situations.

---

## Comment 1

> Username: mborland  
> Created at: 2024-01-19 07:45:33 UTC  
> Url: https://github.com/boostorg/charconv/issues/124#issuecomment-1899916867  

I believe I hit all of your comments in: https://github.com/cppalliance/charconv/pull/127. The same comments can be applied to the `to_chars` section so I will make the changes there as well.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-19 09:54:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/124#issuecomment-1900091733  

Are you sure https://github.com/cppalliance/charconv/pull/127/commits/49c54b4d0f179d1aa1cc9e237ea2f88a920b9e79 addresses the issue? I cannot see any occurrence of word `limits` in the commit.

---

## Comment 3

> Username: mborland  
> Created at: 2024-01-19 10:11:37 UTC  
> Url: https://github.com/boostorg/charconv/issues/124#issuecomment-1900119546  

> Are you sure https://github.com/cppalliance/charconv/pull/127/commits/49c54b4d0f179d1aa1cc9e237ea2f88a920b9e79 addresses the issue? I cannot see any occurrence of word `limits` in the commit.  
  
See https://github.com/cppalliance/charconv/commit/18ca2fcb6de6eac81121987418fde0a87d968d7f. Same PR but different commit.
