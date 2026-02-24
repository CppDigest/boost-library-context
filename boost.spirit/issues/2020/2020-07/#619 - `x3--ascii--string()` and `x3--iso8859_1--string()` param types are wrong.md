# #619 - `x3::ascii::string()` and `x3::iso8859_1::string()` param types are wrong [Open]

> Username: dodheim  
> Created at: 2020-07-26 12:26:52 UTC  
> Updated at: 2025-05-10 00:29:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/619  

[Both of these are using `wchar_t` at present](https://github.com/boostorg/spirit/blob/a1ace8d8eb614785c6f619a46af13c1f032b8cd0/include/boost/spirit/home/x3/string/literal_string.hpp#L138-L192), which is plainly incorrect, but the fix for iso8859_1 is unclear to me or there would be a PR to accompany this.  
  
For ascii, I assume that changing `string()` to use `char` where it presently uses `wchar_t` is sufficient; and I assume that `lit()` taking a `basic_string` of any char type rather than being restricted to `char` is acceptable, but I'm calling attention to it just in case.  
  
For iso8859_1, however, `char_encoding::iso8859_1::char_type` is `unsigned char` – the `string()` overload taking a C-string could be changed to take `unsigned char const*`, but is that correct? I ask because of the following overload, which cannot take a `basic_string<unsigned char>` because there is no specialization of `std::char_traits<unsigned char>`; if that overload must instead be `basic_string<char>` then maybe the C-string literal should be `char const*`. And as for `iso8859_1::lit()`, is the overload taking a `basic_string` of any char type still appropriate? (Also lastly, `literal_string.hpp` fails to include `char_encoding/iso8859_1.hpp`.)  
  
At this point I can make no guesses and must defer to the authors. I'm happy to submit a PR once the answer is settled. :-]

---

## Comment 1

> Username: djowel  
> Created at: 2020-07-26 14:24:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/619#issuecomment-663994280  

Do you have some use cases you can share that reinforces what you are trying to point out? Use cases, esp. those that present some errors or problem points, should make it demonstrably clear what you are trying to explain. For example, why is wchar_t  "plainly incorrect" for ascii?

---

## Comment 2

> Username: dodheim  
> Created at: 2020-07-26 17:47:25 UTC  
> Updated at: 2020-07-26 17:51:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/619#issuecomment-664019535  

I don't have any use cases, per se; I haven't gotten that far along yet because I can't get what seems like "hello world" to compile. :-]  
  
- `char_encoding::ascii::char_type` is `char`  
- `x3::ascii::lit()` ignores `char_type` but is hardcoded to work in terms of `char` literals (but also allows any character `basic_string`)  
- `x3::symbols_parser` uses `char_type`, thus `x3::ascii::symbols` works exclusively in terms of `char`  
- `x3::literal_string` uses `char_type`, thus `x3::ascii::string()` yields an attribute of `std::string` even though it expects `wchar_t` data  
- `x3::ascii::string("")` fails to compile, which is at least unintuitive  
- AFAICT attempting to parse any `char`-based sequence with `x3::ascii::space()` is bound to fail to compile due to deduction failures in `x3::case_compare` and `x3::no_case_compare` (due to trying to compare different character types). E.g. the following fails:  
  ```c++  
  char const *b = /**/, *e = /**/;  
  std::string attr;  
  x3::parse(b, e, x3::ascii::string(L"foo"), attr);  
  ```  
  
I'm happy to be missing something here, and this doesn't affect my code directly as I ended up wanting a different string type with `literal_string` anyway, but the inconsistencies in the first four alone were enough to make me think 'bug', muchless the compile failures.  
  
Thanks for your time, and libraries, Joel!

---

## Comment 3

> Username: djowel  
> Created at: 2020-07-27 00:40:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/619#issuecomment-664063788  

> Thanks for your time, and libraries, Joel!  
  
Most welcome!   
  
Hey, can you make that into an MVCE? I think you are onto something, but I think more clearly when looking at, and actually trying code, tracing though the errors.

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-02-21 18:27:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/619#issuecomment-782902863  

> [Both of these are using `wchar_t` at present](https://github.com/boostorg/spirit/blob/a1ace8d8eb614785c6f619a46af13c1f032b8cd0/include/boost/spirit/home/x3/string/literal_string.hpp#L138-L192), which is plainly incorrect  
  
I completely agree with this, there is also https://github.com/boostorg/spirit/blob/a1ace8d8eb614785c6f619a46af13c1f032b8cd0/include/boost/spirit/home/x3/string/literal_string.hpp#L72-L78 which should not really accept anything except `char` and I suspect recently was the point behind https://github.com/boostorg/spirit/pull/649.  
  
> - `char_encoding::ascii::char_type` is `char`  
  
That's right.  
  
> - `x3::ascii::lit()` ignores `char_type` but is hardcoded to work in terms of `char` literals (but also allows any character `basic_string`)  
  
I am sure the best decision is to allow only `char`, as I already said above.  
  
> - `x3::literal_string` uses `char_type`, thus `x3::ascii::string()` yields an attribute of `std::string` even though it expects `wchar_t` data  
  
Yeah, that's a bug. Probably a copy paste error.  
  
> - `x3::ascii::string("")` fails to compile, which is at least unintuitive  
  
This surprised me quite a lot. Our test https://github.com/boostorg/spirit/blob/c4274111d8284abe30f0be23374555dbecc2cce6/test/x3/lit1.cpp#L54-L55 is subverted by a sneaky https://github.com/boostorg/spirit/blob/c4274111d8284abe30f0be23374555dbecc2cce6/test/x3/lit1.cpp#L20  
  
> - AFAICT attempting to parse any `char`-based sequence with `x3::ascii::space()` is bound to fail to compile due to deduction failures in `x3::case_compare` and `x3::no_case_compare` (due to trying to compare different character types). E.g. the following fails:  
>   ```c++  
>   char const *b = /**/, *e = /**/;  
>   std::string attr;  
>   x3::parse(b, e, x3::ascii::string(L"foo"), attr);  
>   ```  
  
The `x3::ascii::space` is tested here https://github.com/boostorg/spirit/blob/c4274111d8284abe30f0be23374555dbecc2cce6/test/x3/char_class.cpp#L27-L63 so it should work, and in your example you are using `x3::ascii::string(L"foo")`, which I think should not compile anyway.
