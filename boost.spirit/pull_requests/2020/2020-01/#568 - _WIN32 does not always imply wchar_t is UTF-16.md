# #568 _WIN32 does not always imply wchar_t is UTF-16 [Merged]

> Username: puetzk  
> Created at: 2020-01-30 05:16:03 UTC  
> Updated at: 2020-07-23 12:45:27 UTC  
> Merged at: 2020-01-30 07:08:41 UTC  
> Closed at: 2020-01-30 07:08:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/568  

Upon upgrading to from 1.67.0 to boost 1.72.0, I just discovered #413 has broken builds for us, albiet on a fairly exotic platform (using spirit::qi in a [winelib](https://wiki.winehq.org/Winelib) project)  
  
---  
  
Win32 only defines the types from wtypes.h, like WCHAR, LPWCSTR, etc.  
These may or may not be the same thing as wchar_t.  
  
MinGW, cygwin, and wineg++ all support `-f(no-)short-wchar`,  
with the caveat that libstdc++ must be compiled with the same option.  
Doing so is quite unusual for MinGW or cygwin, but more common for wineg++  
as it enables building a winelib app with system glibc/libstdc++.  
Win32's WCHAR is then unsigned short, or with C++11 perhaps char16_t.  
  
MSVC does explicitly document that its wchar_t is always UTF16:  
https://docs.microsoft.com/en-us/cpp/cpp/char-wchar-t-char16-t-char32-t?view=vs-2019  
  
C99/C++11 compilers should provide `__STDC_ISO_10646__` if wchar_t is unicode  
  
GCC, Clang, and ICC all provide `__SIZEOF_WCHAR_T__` to distinguish  
-fshort-wchar (defaulted by mingw/cygwin) from -fno-short-wchar  
https://gcc.gnu.org/onlinedocs/gcc-9.2.0/cpp/Common-Predefined-Macros.html  
  
This takes the approach of assuming that a 2-byte unicode wchar_t  
might be UTF-16 (and still works if its's actually UCS-2, it just never  
finds any surrogate pairs), and 4-byte unicode must be UCS-4.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-02-05 18:02:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-582535120  

What is the purpose of testing `__STDC_ISO_10646__`? My test shows that it is always defined on Linux (Clang/GCC/ICC) and always undefined on Windows (Clang/ICC/MinGW/MSYS2/MSVC).  
  
From my point of view the check should be `(defined(_MSC_VER) || defined(__WCHAR_WIDTH__) && (__WCHAR_WIDTH__ < 21)) && defined(BOOST_SPIRIT_UNICODE)` (21-bit requirement is from https://www.unicode.org/faq//utf_bom.html#utf32-2), but because for me `-fno-short-wchar` has no effect on MSYS2 (Cygwin) or MinGW (always   
 `__STDC_ISO_10646__` is not defined and `__SIZEOF_WCHAR_T__ == 2`), and Clang does not support that flag -- I may be missing your case.

---

## Comment 2

> Username: puetzk  
> Created_at: 2020-02-06 03:19:05 UTC  
> Updated_at: 2020-02-06 03:19:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-582717351  

I was just being pedantic. It's the official (inherited from C99) way of saying that "my wchar_t is unicode" (and its value specifies which revision of unicode). Because _technically_ a standard library could cook up their own 16-bit encoding extending EBCDIC, or something perverse like that, and it *would* be standards conforming as long as they don't set `__STDC_ISO_10646__`. But I'm not aware of any realistic implementation that actually does anything so pointlessly incompatible, so it really isn't doing anything useful.  
  
I think you actually have to configure a mingw-64 bootstrapped with matching libc/libstdc++ to have a `-fno-short-wchar` mingw. Which would also be a dumb thing to create, alibet possible :-)  
  
As I said, my realistic use case was wing++, so a normal linux g++ and libstdc++ (defaulting to `-fno-short-wchar` and a UCS-4 `wchar_t`), but with `_WIN32` defined by the wine headers, which was incorrectly falling into your assumption that `wchar_t` and `WCHAR` were the same type and both UTF-16. So it's `__SIZEOF_WCHAR_T__` that's doing the useful work for me.  
  
I didn't refer to `__WCHAR_WIDTH__` because it is explicitly not public:  
https://gcc.gnu.org/onlinedocs/gcc-9.2.0/cpp/Common-Predefined-Macros.html  
> Defined to the bit widths of the corresponding types. They exist to make the implementations of limits.h and stdint.h behave correctly. You should not use these macros directly; instead, include the appropriate headers.  
  
For glibc, one one could include `<stdint.h>` and then check `WCHAR_WIDTH` (which is public). https://www.gnu.org/software/libc/manual/html_node/Width-of-Type.html. But that macro is not called for by the C++ standard, so another libc might or might not include it. So I just settled For `__SIZEOF_WCHAR_T__` with only compiler-dependencies (and those met by all of Boost's compilers)

---

## Comment 3

> Username: puetzk  
> Created_at: 2020-02-06 03:52:37 UTC  
> Updated_at: 2020-02-06 03:53:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-582724437  

Ok, went back to re-read and I concur that checking for `__STD_ISO_10646__` is wrong. The wording was expanded/clarified in newer versions from what I had remembered.  
  
in C99  
  
> An integer constant of the form __yyyymmL__ (for example, 199712L), intended to indicate that values of type wchar_t are the coded representations of the characters defined by ISO/IEC 10646, along with all amendments and technical corrigenda, as of the specified year and month  
  
in C11, and C++11/14/17  
  
> An integer constant of the form __yyyymmL__ (for example, 199712L). If this symbols is defined, then every character in the Unicode required set, when stored in an object of type `wchar_t`, has the same value as the short identifier of that character. The _Unicode required set_ consists of all characters that are defined by ISO/IEC 10646, along with all amendments and technical corrigenda, as of the specified year and month. If some other encoding is used, the macro shall not be defined and the actual encoding used is implementation-defined.  
  
and the "short identifier" in ISO 10646 is   
> "the sequence of six hexadimal digits that represents the code point of the character" (and then goes on to say you can drop leading zeros, or add the "U+", but it's clear enough that together these mean the no-op encoding where you just store the code point an integer.  
  
Oops. The new wording seems unambiguous that a variable-length encoding like UTF-16 does *not* qualify for this macro to be set (since characters above the BMP are divided into surrogate pairs, and *not* directly stored as their code-point value). Which matches the behavior you were seeing (that nobody set it on windows).  
  
Which, since "is UTF-16" is the case we were trying to distinguish to enable this handler, means checking for that macro was simply wrong. Mea culpa. It doesn't appear I can reopen this PR since it's merged, so do you want me to file another, or can would you prefer (since you seem to be a collaborator) to just make the fix mentioning this number to keep the discussion/rationale linked in one place?  
  
As long as it retains some form of checking for `-fno-short-wchar` (e.g. `__SIZEOF_WCHAR_T__` or `WCHAR_WIDTH`) it should work for my use case with wineg++.  
  
And, apparently g++/wineg++ technically ought not to set this define when using `-fshort-wchar` on linux (or at least 199306 is the largest conforming value; it *could* claim to be UCS-2 before surrogates were invented in Unicode 2.0 in 199607, albiet that's not very useful)

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-02-06 12:43:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-582888736  

`__WCHAR_WIDTH__` will always be defined when `WCHAR_WIDTH` is defined by `cstdint`, and as long as they are both extensions (and `__SIZEOF_WCHAR_T__` too) I would prefer to use the one that does not bring additional includes.  
  
Possibly `WCHAR_MAX - WCHAR_MIN < 0x10FFFFu` is what we need here, but I am not sure about portability (it might overflow).

---

## Comment 5

> Username: puetzk  
> Created_at: 2020-02-06 18:38:25 UTC  
> Updated_at: 2020-02-06 18:38:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-583048896  

Another possible (and standards-conforming) check I thought of would be to SFINAE `enable_if` these functions, instead of prepossessing them. That could be based on a constexpr function actually comparing a non-BMP string like L"\U0001f34c" (🍌) to the utf-16 surrogate pairs 0xD83C 0xDF4C.  In practice just detecting based on the size of the result (either 2 or 3, depending on whether you have one wchar_t or two surrogate halves before the null-terminator) would probably suffice, but if you actually check the encoding it becomes very explicit.  
  
But if I recall spirit 2 aims to support quite old compilers, so I'm not sure if constexpr (or for that mattoer `\U` literals) are fair game.

---

## Comment 6

> Username: puetzk  
> Created_at: 2020-02-06 19:12:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-583063432  

Actually, you don't really need the constexpr function; just inlining the expression `(L"\U0001f34c"[0] == 0xD83C) && (L"\U0001f34c"[1] == 0xDF4C) && L"\U0001f34c"[2] == 0` ought to suffice.  
  
But G++4.5 (and presumably older) rejects this with   
> an array reference cannot appear in a constant-expression  
  
Which appears to be related to DR366: http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_defects.html#366 (though later relaxed as part of c++11 constexpr).  
  
And I found http://boost-spirit.com/home/info/spirit-v2-x-compiler-compatibility/ suggesting the target is still all the way back to 3.4 (unless that's an outdated reference?)

---

## Comment 7

> Username: Kojoley  
> Created_at: 2020-02-06 19:22:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-583067736  

> But if I recall spirit 2 aims to support quite old compilers, so I'm not sure if constexpr (or for that mattoer \U literals) are fair game.  
  
It supports C++03, so yes, no constexpr  
  
> And I found http://boost-spirit.com/home/info/spirit-v2-x-compiler-compatibility/ suggesting the target is still all the way back to 3.4 (unless that's an outdated reference?)  
  
Boost regression system is testing on GCC 4.6+, Clang 3.4+, MSVC 10+ (https://www.boost.org/development/tests/develop/developer/summary.html) everything except these are may happen to work but is not guaranteed. The repository main page has a list of supported compilers (https://github.com/boostorg/spirit#spirit-v2-2nd-generation) but it is also may be already outdated as those runners were removed since then.  
  
> constexpr function actually comparing a non-BMP string like L"\U0001f34c" (banana) to the utf-16 surrogate pairs 0xD83C 0xDF4C.  
> Actually, you don't really need the constexpr function; just inlining the expression (L"\U0001f34c"[0] == 0xD83C) && (L"\U0001f34c"[1] == 0xDF4C) && L"\U0001f34c"[2] == 0 ought to suffice.  
  
Can you please test `#if L'\U0010ffff' == 0x10FFFFu` macro?

---

## Comment 8

> Username: puetzk  
> Created_at: 2020-02-06 19:41:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-583075707  

> Can you please test #if L'\U0010ffff' == 0x10FFFFu  
  
That's implementation-defined behavior in C++03 section 2.13.3 (2) (wording basically unchanged in 11/14/17)  
  
> The value of a wide-character literal containing multiple c-chars is implementation-defined  
  
For g++ 5.2 and 7 (the two I originally had problems with), the comparison is true for -fno-short-wchar (ucs4), and false for -fshort-wchar (utf16), as desired but gives a warning in the latter case  
> test.cpp:1:5: warning: character constant too long for its type  
  
It evaluated the out-of-range literal to 0xdfff (Low surrogate, last)  
  
cl 19.24 (VS2019) is false as it should be, also with a warning:  
  
> warning C4066: characters beyond first in wide-character constant ignored  
  
It evaluated the character literal to 0xd83c (so it just returned the first surrogate half)  
  
But L'\Uxxxxxxx' is itself already a C++11-ism.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2020-02-06 20:09:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-583087213  

> > Can you please test #if L'\U0010ffff' == 0x10FFFFu  
>  
> That's implementation-defined behavior in C++03 section 2.13.3 (2) (wording basically unchanged in 11/14/17)  
  
Yes, and we are trying to determine it :-)  
  
> > The value of a wide-character literal containing multiple c-chars is implementation-defined  
>  
> For g++ 5.2 and 7 (the two I originally had problems with), the comparison is true for -fno-short-wchar (ucs4), and false for -fshort-wchar (utf16), as desired but gives a warning in the latter case  
  
That's great!  
  
> > test.cpp:1:5: warning: character constant too long for its type  
  
The check is actually checking that, we just need a way to suppress the warning :-)  
  
> But L'\Uxxxxxxx' is itself already a C++11-ism.  
  
It is C99 also, and since it is supported even in VS2008/GCC4.1/Clang3.0 -- we can live with it in C++03 mode like we did for a long time with variadic macros, I think.

---

## Comment 10

> Username: puetzk  
> Created_at: 2020-03-06 14:22:07 UTC  
> Updated_at: 2020-03-06 20:33:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-595789137  

Did we get any consensus here? I concur that my use of `__STDC_ISO_10646__` was wrong, and will break this for (at least) ICC/MinGW on Win32. Should I just open another PR removing that part (keeping just `#if defined(_MSC_VER) || (__SIZEOF_WCHAR_T__ == 2)`, which seems to cover realistic platforms but could very hypothetically fail on a perverse platform with a 2-byte but non-UTF encoding, or did @Kojoley have a idea to deal with the warnings using his idea of L'\U0010ffff' (which is a very nice solution if it can be made to work...)?

---

## Comment 11

> Username: puetzk  
> Created_at: 2020-03-06 14:30:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-595792826  

Or, since @Kojoley thinks "\Uxxxxxxx" is sufficiently portable (despite being officially C++11), we could pursue the idea of doing this with enable_if instead of #ifdef, in which case it could be a string instead of a char literal and there wouldn't be a warning.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2020-03-06 14:45:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-595799492  

I was working on the issue  https://github.com/Kojoley/spirit/commit/e3551477467cc2c8d5c132196b82e6b7db85945c but I have not made it to work with `-fwide-exec-charset` (is it even worth supporting?).

---

## Comment 13

> Username: puetzk  
> Created_at: 2020-07-23 12:45:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/568#issuecomment-662985563  

Just to close out the conversation here, @Kojoley did compete and merge a fix: 785a5f35d23c6d9b429e81a46578b534b4787520

---
