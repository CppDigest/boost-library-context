# #746 fix wrong buffer size in path string conversion functions [Merged]

> Username: striezel  
> Created at: 2024-05-05 16:16:39 UTC  
> Updated at: 2024-05-13 21:57:54 UTC  
> Merged at: 2024-05-13 07:59:00 UTC  
> Closed at: 2024-05-13 07:59:00 UTC  
> Url: https://github.com/boostorg/gil/pull/746  

### Description  
  
Fixes #733.  
  
Issue:  
The length calculation of the destination buffers for conversions from `std::wstring` to `std::string` or `wchar_t*` to `char*` is incorrect, causing a buffer overflow in some cases.  
  
Explanation:  
The current code uses `wcslen()` to "calculate" the length of the converted destination string buffer. However, `wcslen()` is just a `strlen()` for `wchar_t*` strings. So it returns the number of wide characters in a `wchar_t*` string, but this does not need to be the same as the number of narrow characters (that is: `char`) in the converted string (`char*` or `std::string`). It just happens to be the same, if the wide character string only uses characters of the English alphabet where each wide character is converted to exactly one narrow character.  
  
So the length has to be calculated properly. There are (at least) two possibilities:  
* Use `wcstombs()`'s POSIX extension to calculate the length before doing the conversion.  
  
  > POSIX specifies a common extension: if `dst` is a null pointer, this function returns the number of bytes that would be written to `dst`, if converted.  
  
  But that is an extension and cannot be relied upon to be present.  
* We should use [`wcsrtombs()`](https://en.cppreference.com/w/cpp/string/multibyte/wcsrtombs) instead, where that behaviour is not an extension but part of the function and we do not need to rely on the presence of an extension. This is what this pull request does.  
  
### References  
  
* https://github.com/boostorg/gil/issues/733#issuecomment-2094864132  
* [wcsrtombs() on cppreference.com](https://en.cppreference.com/w/cpp/string/multibyte/wcsrtombs)  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Commented]

> Username: Osyotr  
> Created_at: 2024-05-05 16:41:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/746#pullrequestreview-2039735656  

📁 include/boost/gil/io/path_spec.hpp

```diff
  46 |+     const wchar_t* str = s.c_str();
  47 |+     const std::size_t len = std::wcsrtombs(nullptr, &str, 0, &state);
  48 |+     char* c = reinterpret_cast<char*>( alloca( len + 1));
```

> Username: Osyotr  
> Created_at: 2024-05-05 16:41:55 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1590354273  

Is `alloca` really needed here?

> Username: striezel  
> Created_at: 2024-05-05 16:57:47 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1590358162  

I don't really have an opinion about it. What would you use instead? `alloca` was used in the original code and I just kept it.  
  
The thing is, we need some kind of `char*` for `wcstombs()` to write to, and `alloca` just allocates the temporary buffer here and frees it once the conversion is done. It's kind of convenient, because there is no need to free the buffer explicitly - so there is no chance to leak the buffer.

> Username: Osyotr  
> Created_at: 2024-05-05 17:34:50 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1590370500  

There's already a buffer in form of `std::string`.

> Username: striezel  
> Created_at: 2024-05-05 17:57:45 UTC  
> Updated_at: 2024-05-05 17:58:39 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1590374637  

No, not (yet) at this point. The `std::string` is only created three lines further down.  
  
So let's say we decide to move that up and create a string with the proper length before the call to `wcstombs()`. Then we would still need to call `c_str()` on that string to get the "buffer" and then write to that. Looks like a possible solution, but [cppreference says](https://en.cppreference.com/w/cpp/string/basic_string/c_str):  
  
> Writing to the character array accessed through `c_str()` is undefined behavior.  
  
And the last thing you want is more UB in your code.  
  
Then there's `data()`, too, but this is essentially the same as `c_str()`:  
  
> c_str() and data() perform the same function. (since C++11)  
  
So chances are that is UB, too.  
  
I can understand the desire to get rid of the allocation, but if I have to decide between one more allocation or risking undefined behaviour, then I take the allocation.

> Username: Osyotr  
> Created_at: 2024-05-05 21:16:17 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1590416651  

Ahh the non-const .data() overload is C++17 feature.   
There's always `&str[0]` ;)

> Username: striezel  
> Created_at: 2024-05-06 22:52:28 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1591632251  

> Ahh the non-const .data() overload is C++17 feature.  
  
Indeed.  
  
> There's always `&str[0]` ;)  
  
Feels a bit hacky to me, I did it to remove the `alloca()` call.


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2024-05-09 22:18:40 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2103501755  

I dont know much about multibyte string conversions, but I will review it to the best of my ability tomorrow.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2024-05-10 06:58:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/746#pullrequestreview-2049390835  

LGTM. Big thanks @striezel

📁 include/boost/gil/io/path_spec.hpp

```diff
   1 | //
   2 | // Copyright 2007-2008 Andreas Pokorny, Christian Henning
   3 |+ // Copyright 2024 Dirk Stolle (minor fixes)
```

> Username: mloskot  
> Created_at: 2024-05-10 06:57:41 UTC  
> Updated_at: 2024-05-10 06:58:00 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1596344339  

@striezel IMO  `(minor fixes)` note is not necessary

> Username: striezel  
> Created_at: 2024-05-10 21:33:20 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1597247409  

Alright. The note has been removed in commit a27fc03b526d2c45c337815f40f9f1001490657b.


---

## Review 4 [Changes requested]

> Username: simmplecoder  
> Created_at: 2024-05-10 21:18:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/746#pullrequestreview-2050886079  

Thanks for the commits, I learned a lot from reading the description. From the include chain I couldn't find `#include <cwchar>`, could you please add that? `std::wcsrtombs` seems to be declared there.  
I looked at `std::codecvt` as possible C++ refactoring, but unfortunately it was disappointing.

📁 include/boost/gil/io/path_spec.hpp

```diff
  47 |+     const std::size_t len = std::wcsrtombs(nullptr, &str, 0, &state);
  48 |+     std::string result(len, '\0');
  49 |+     wcstombs( &result[0], s.c_str(), len );
```

> Username: simmplecoder  
> Created_at: 2024-05-10 21:15:00 UTC  
> Updated_at: 2024-05-10 21:18:53 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1597236300  

`std::wcstombs` implied?

> Username: striezel  
> Created_at: 2024-05-10 21:20:43 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1597240170  

Yes. `wcstombs` is `std::wcstombs`.

> Username: striezel  
> Created_at: 2024-05-10 21:28:46 UTC  
> Url: https://github.com/boostorg/gil/pull/746#discussion_r1597244830  

Added `std::` for clarity.


---

## Comment 5

> Username: striezel  
> Created_at: 2024-05-10 21:19:57 UTC  
> Updated_at: 2024-05-10 21:34:49 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2105284664  

> I dont know much about multibyte string conversions, but I will review it to the best of my ability tomorrow.  
  
Just to explain the basics (may not be completely accurate, but it should be enough to get a basic understanding):  
In this code, there are two different kinds of string: wide strings using `wchar_t` for character representation (used in `std::wstring` or C-style `wchar_t*` strings) and narrow strings using `char` for character representation (used by `std::string` or C-style `char*` strings). On Windows systems `wchar_t` is 16 bits and contains UTF-16 code units. On other systems it may be 32 bits and contain UTF-32, but in practice it is always more than 8 bits. In contrast, `char` only has 8 bits and therefore usually contains UTF-8 code units. (A narrow string could also contain ASCII, ISO-8859-1, etc., but listing and explaining all possibilities would go to far here. Therefore, let's assume that it is UTF-8.)  
  
So when a string is converted from a wide string (`wchar_t` / UTF-16) to a narrow string (`char` / UTF-8) the strings do not necessarily have the same number of code points. If a string only uses letters from the English alphabet, then both wide and narrow string can have the same length, because one `wchar_t` can be converted to exactly one `char`. However, when the wide strings use characters from a more "exotic" alphabet, say Cyrillic letters or or Chinese letters, the situation is different. A Cyrillic or Chinese letter will only use one code unit in UTF-16, that is it can be represented by a single `wchar_t`. But in UTF-8 that same letter will need multiple code units (two or three), meaning it needs more than one `char` to be represented properly. That is why one `wchar_t` may be converted to more than one `char` and the length of those strings can differ after conversion. Therefore, the length of the buffer for the converted string has to be calculated before conversion. Otherwise it may be too short to hold the entirety of the converted string.  
  
Here, the length calculation is done by [`wcsrtombs()`](https://en.cppreference.com/w/cpp/string/multibyte/wcsrtombs), and then the actual conversion is done by [`wcstombs()`](https://en.cppreference.com/w/cpp/string/multibyte/wcstombs).

---

## Comment 6

> Username: simmplecoder  
> Created_at: 2024-05-10 21:29:48 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2105294072  

Thanks for the explanation. I suppose the easiest solution is to throw boost.locale at it to completely nuke it, but I am not sure if it is worth it. I suppose people can open their streams and pass those if they want to do anything exotic.

---

## Comment 7

> Username: striezel  
> Created_at: 2024-05-10 21:31:57 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2105296164  

> Thanks for the commits, I learned a lot from reading the description. From the include chain I couldn't find `#include <cwchar>`, could you please add that? `std::wcsrtombs` seems to be declared there.  
  
You are right. `<cwchar>` is where the function is declared, so I added it.  
(It's a bit surprising that the CI passed on all those GCC, Clang and MSVC variants without it.)  
  
> I looked at `std::codecvt` as possible C++ refactoring, but unfortunately it was disappointing.  
  
Yes. As far as I remember, parts of that are deprecated in newer C++ standards.

---

## Comment 8

> Username: striezel  
> Created_at: 2024-05-10 22:00:07 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2105320058  

> I suppose the easiest solution is to throw boost.locale at it to completely nuke it, but I am not sure if it is worth it. I suppose people can open their streams and pass those if they want to do anything exotic.  
  
As far as I understand it, some parts of Boost.Locale also need the ICU library (International Components for Unicode) and that library is ca. one order of magnitude larger than Boost.Locale itself (counting file sizes for installed libraries here). Furthermore, GIL does not have any dependency on Boost.Locale as of now, so this would potentially add two new dependencies to GIL. That kind of overhead may not be acceptable to some users of Boost.GIL, especially when we can have a smaller solution with the help of the standard library.

---

## Comment 9

> Username: mloskot  
> Created_at: 2024-05-13 07:57:48 UTC  
> Url: https://github.com/boostorg/gil/pull/746#issuecomment-2106896592  

@striezel   
>   
> Furthermore, GIL does not have any dependency on Boost.Locale as of now, so this would potentially add two new dependencies to GIL. That kind of overhead may not be acceptable to some users of Boost.GIL, especially when we can have a smaller solution with the help of the standard library.  
  
I agree, we should try to keep the list of required dependencies as short as possible.  
But, I don't see any issue with introducing optional dependencies for features that can be controlled via CMake option/conditional compilation. The only issue I see is the potential maintenance overhead.

---
