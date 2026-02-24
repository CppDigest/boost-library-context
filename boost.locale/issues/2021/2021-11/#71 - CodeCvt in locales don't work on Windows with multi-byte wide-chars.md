# #71 - CodeCvt in locales don't work on Windows with multi-byte wide-chars [Closed]

> Username: Flamefire  
> Created at: 2021-11-17 18:52:19 UTC  
> Updated at: 2022-09-16 09:31:17 UTC  
> Closed at: 2022-09-16 09:31:17 UTC  
> Url: https://github.com/boostorg/locale/issues/71  

I see a failing test for `test_ok<Char>("\xf0\xa0\x82\x8a",g("en_US.UTF-8")); // U+2008A` which I traced down to Boost.Locale ultimately using `std::codecvt<wchar_t, char, mbstate_t>` which on VS 2019 uses `_Mbrtowc` under the hood which can only decode to a single `wchar_t` but the above would decode to a surrogate pair. Hence the decoding of that UTF-8 char fails which makes the read fail and the string read from the so-imbued file fails

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-05-23 13:32:21 UTC  
> Updated at: 2022-05-23 14:48:25 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1134686007  

@artyom-beilis Any comment on how to handle this? I.e. simply declare only UCS2 (i.e. UTF codepoints which can be represented in 2 bytes) as supported?  
  
See also https://github.com/microsoft/STL/pull/1815#discussion_r610855606  
> Background: _Mbrtowc is a wrapper around a call to MultiByteToWideChar which relies on the anachronistic assumption that any encoded character can be converted to a single UCS-2 code unit. This is clearly wrong and has been for many years [...]

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2022-05-24 10:06:58 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135705666  

Its strange. Because it used to work. The `mbstate_t` is just a place to store the state. It worked once for sure.  
  
It should support UTF-16

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2022-05-24 10:10:07 UTC  
> Updated at: 2022-05-24 10:10:24 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135710506  

It should not use `std::codecvt<wchar_t, char, mbstate_t>` but rather local's codecvt faced derived from it.  
  
Something is wrong. Unofrtunatelly I don't have VC-2019 or even partially working windows dev environment. :-(

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2022-05-24 10:13:21 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135715384  

I recall several failures with this `test_ok` and usually they were related to `iostream` libraries. IIRC there were some clang issues. It was long time ago

---

## Comment 5

> Username: Flamefire  
> Created at: 2022-05-24 12:04:15 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135830234  

> It should not use std::codecvt<wchar_t, char, mbstate_t> but rather local's codecvt faced derived from it.  
  
Yes and that is created at https://github.com/boostorg/locale/blob/9259845c6fd95ce6b8bf11e8ec5d8bdf38836c4e/src/std/codecvt.cpp#L37  
  
which for the MS STL yields a small effectively the `std::codecvt` above (actually a derived class with some insignificant meta data for this issue). So we get to this class: https://github.com/microsoft/STL/blob/0349ce1540c21952733d0f2bf1dfe85f4b9cd749/stl/inc/xlocale#L1909  
This uses `_Mbrtowc` at https://github.com/microsoft/STL/blob/0349ce1540c21952733d0f2bf1dfe85f4b9cd749/stl/inc/xlocale#L1984  
And that fails for UTF-16 surrogate pairs: https://github.com/microsoft/STL/blob/20288f94e4b1319bfcf2874ba3a6157b5cc6172c/stl/src/xmbtowc.cpp#L110-L115  
  
The issue is that they simply ignore the state. So the MS `std::codecvt<wchar_t, char, mbstate_t>` doesn't use UTF-16 but their UCS-2 which is basically unencoded (up to) 2-byte unicode. So codepoints above U+FFFF won't work with the std backend on MS STL

---

## Comment 6

> Username: artyom-beilis  
> Created at: 2022-05-24 12:39:22 UTC  
> Updated at: 2022-05-24 12:39:38 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135868623  

I don't understand.   
  
I override `do_in` with code that handles utf16 correctly

---

## Comment 7

> Username: artyom-beilis  
> Created at: 2022-05-24 12:48:36 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135879099  

Is this code is called: https://github.com/boostorg/locale/blob/develop/include/boost/locale/generic_codecvt.hpp#L239

---

## Comment 8

> Username: Flamefire  
> Created at: 2022-05-24 12:57:37 UTC  
> Updated at: 2022-05-24 12:57:47 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1135889611  

No it is not. What happens:  
- `g("en_US.UTF-8")` in `test_ok`  
- install a `codepage_facet`  
- `create_codecvt`  
- `return codecvt_bychar<wchar_t>`  
  
The custom codecvt is not used because `utf_mode_ = utf8_native_with_wide` as `std::locale("en_US.UTF-8")` works, see https://github.com/boostorg/locale/blob/9259845c6fd95ce6b8bf11e8ec5d8bdf38836c4e/src/std/std_backend.cpp#L114  
  
Maybe the `loadable(lid)` used to NOT work?

---

## Comment 9

> Username: artyom-beilis  
> Created at: 2022-05-24 16:22:30 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1136135908  

> Maybe the `loadable(lid)` used to NOT work?  
  
Absolutely!  
  
Under MSVC you couldn't create `std::locale("en_US.UTF-8")` it looks something new indeed.

---

## Comment 10

> Username: Flamefire  
> Created at: 2022-05-24 16:57:20 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1136181030  

So what would you like to do? Check to use the Windows codepage name first?

---

## Comment 11

> Username: artyom-beilis  
> Created at: 2022-05-24 17:43:30 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1136253946  

> So what would you like to do? Check to use the Windows codepage name first?  
  
I would do this, in any case of MSVC even if codepage is loadable I'd use `utf8_from_wide` or at least for covecvt only (in case MSVC really implemented utf-8 locales) since MSVC's UTF-8/Wide codecvt is broken so needs to be replaced.  
  
I'm really-really grateful for an amazing job you do. You really saving this project since I just can't keep-up with all latest bells and whistles from MSVC team.

---

## Comment 12

> Username: Flamefire  
> Created at: 2022-05-25 18:12:21 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1137662878  

> I'm really-really grateful for an amazing job you do. You really saving this project since I just can't keep-up with all latest bells and whistles from MSVC team.  
  
That's why CI is so very important. It also allows comparison between versions of the code and/or compiler to see when things broke. Right now the testsuite fails pretty much everywhere and I barely know enough about the intentions of the code to guess a fix. See e.g. #69 or the runs at #76  
  
Hence my efforts to get at least a minimum of that working. And make sure it at least compiles on all (common) platforms.

---

## Comment 13

> Username: Flamefire  
> Created at: 2022-06-13 09:33:41 UTC  
> Updated at: 2022-06-13 12:40:31 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1153694634  

I double-checked everything and it seems the test is broken and expects to much. See the MS comment at https://github.com/microsoft/STL/blob/20288f94e4b1319bfcf2874ba3a6157b5cc6172c/stl/src/xmbtowc.cpp#L110-L115  
  
> // this would result in a UTF-16 surrogate pair, which we can't emit in our  
   // singular output wchar_t, so fail. see N4750 [locale.codecvt.virtuals]/3  
  
And the referenced part of the [standard](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4713.pdf) reads:  
  
> A codecvt facet that is used by basic_filebuf  shall have the property that if  
> `do_out(state, from, from_end, from_next, to, to_end, to_next)`  
> would return ok, where from != from_end, then  
> `do_out(state, from, from + 1, from_next, to, to_end, to_next)`  
> shall also return ok, and that if  
> `do_in(state, from, from_end, from_next, to, to_end, to_next)`  
> would return ok, where to != to_end, then  
> `do_in(state, from, from_end, from_next, to, to + 1, to_next)`  
> shall also return ok  
  
And a footnote reads:  
  
> Informally, this means that `basic_filebuf` assumes that the mappings from internal to external characters is 1 to N: a  
`codecvt` facet that is used by `basic_filebuf` must be able to translate characters one internal character at a time.  
  
And for surrogates, i.e. the tests at https://github.com/boostorg/locale/blob/d1fc12426ffcc411a8ccddcf9ef6f33a2d5d0609/test/test_codepage.cpp#L127-L137, that isn't possible.  
  
However there is a possible solution:  
  
> Note: As a result of operations on state, it can return ok or partial and set  
> from_next == from and to_next != to.  
  
And the `char16_t` `codecvt` from MS indeed does make use of that.  
  
So at least we should have a testcase, checking that the above properties hold true for the Boost.Locale codecvts, e.g. https://github.com/boostorg/locale/blob/11f50e9e3c4e068c9a2cc71e78f0dd4a7c28ecd1/include/boost/locale/generic_codecvt.hpp#L239  
  
If that is the case, we can use our codecvt unconditionally for utf-8 wchar.

---

## Comment 14

> Username: Flamefire  
> Created at: 2022-06-19 10:08:03 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1159677198  

Ok it seems you are not allowed to use a codecvt that "supports" multiple external chars In a std::filebuf according to the standard, I.e. Those multibyte utf-16 codepoints. See the comment from MS at https://github.com/microsoft/STL/issues/2788  
  
We can do "works usually but is actually UB" and use our utf-8 codecvt or remove the tests that test codepoints larger than what fits into a single utf-x char.  
  
@artyom-beilis your library, your decision

---

## Comment 15

> Username: artyom-beilis  
> Created at: 2022-06-19 20:25:44 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1159805490  

I think work with our codecvt - as long as it works. Many things there done according to the practice rather to the book of standard.  
  
Assuming that the same code handles filebuf (which is reasonable to assume) and if it handles `char16_t` correctly no reason why wouldn't it work on `wchar_t`.  
  
Unfortunately standard is horribly broken by design and it is one of the reasons Boost.Locale exists.  
  
So I think better to install our codecvt and give the user power.

---

## Comment 16

> Username: Flamefire  
> Created at: 2022-06-21 16:44:24 UTC  
> Url: https://github.com/boostorg/locale/issues/71#issuecomment-1162002115  

> Assuming that the same code handles filebuf (which is reasonable to assume) and if it handles `char16_t` correctly no reason why wouldn't it work on `wchar_t`.  
  
That is the question: Does `filebuf` handle `char16_t` correctly? I'd say [no](https://en.cppreference.com/w/cpp/locale/codecvt_utf8_utf16):   
> This is an N:M conversion facet, and cannot be used with std::basic_filebuf  
  
> Unfortunately standard is horribly broken by design and it is one of the reasons Boost.Locale exists.  
  
It seems to make working with it hard at least.  
  
Additionally there is this:  
> `std::codecvt<wchar_t, char, std::mbstate_t>` | conversion between the system's native wide and the single-byte narrow character sets  
  
And "the system's native wide character set" on Windows is basically UCS-2 which excludes non-BMP characters (i.e. > U+FFFF), isn't it? Well partially at least as the WinAPI functions seem to be expecting UTF-16 (which is a superset of UCS-2) nowadays.  
  
I'm not sure what a user may expect here and what could break in `filebuf` when we use our UTF-16 codecvt instead of a UCS-2 one.  
  
Also asked on [Stackoverflow](https://stackoverflow.com/questions/72704318/can-i-use-a-stdfstream-with-utf-16-encoding) and in [Slack](https://cpplang.slack.com/archives/C21PKDHSL/p1655815095383019) just to make sure we are not missing anything.
