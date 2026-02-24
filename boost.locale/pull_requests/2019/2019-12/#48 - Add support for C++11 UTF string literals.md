# #48 Add support for C++11 UTF string literals [Closed]

> Username: Flamefire  
> Created at: 2019-12-13 13:47:00 UTC  
> Updated at: 2022-06-05 09:31:47 UTC  
> Closed at: 2022-06-01 20:20:27 UTC  
> Url: https://github.com/boostorg/locale/pull/48  

Use a trait to select the correct encoding.  
Defaults to current 'sizeof(T)' but is specialized for char16_t/char32_t  
  
Note that this is fully backwards compatible except for ` char16_t/char32_t` with sizes different of `2/4` (unlikely but possible)  
  
Fixes #47

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2019-12-13 17:15:42 UTC  
> Url: https://github.com/boostorg/locale/pull/48#issuecomment-565526246  

unless you can give me a real platform that works decently with boost and has sizeof char16/32_t/wchar_t of sizes different than 2/4 I don't care

---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-12-13 17:42:13 UTC  
> Url: https://github.com/boostorg/locale/pull/48#issuecomment-565536446  

I'd normally agree with that such a rare platform will probably not support boost anyway. But this is basically a header-only sublib and UTF conversion might be required there.  
  
In any case isn't `struct utf_traits<CharType, format::UTF8>` nicer? Especially as a user could use that to enforce an encoding without a magic number.  
  
But yeah, it's not critical. Just wanted it to be 100% correct as this is Boost.  
  
PS: The part https://github.com/boostorg/locale/pull/48/files#diff-e9eaf72013da6af4bb07d46b54e1fbfdL252-R309 is still useful as unrelated. It fixes a C&P error in the tests. Also the function names are reversed O.o

---
