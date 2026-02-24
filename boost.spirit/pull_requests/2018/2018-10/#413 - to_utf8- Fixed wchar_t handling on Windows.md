# #413 to_utf8: Fixed wchar_t handling on Windows [Merged]

> Username: Kojoley  
> Created at: 2018-10-28 15:00:58 UTC  
> Updated at: 2018-10-30 11:48:56 UTC  
> Merged at: 2018-10-30 11:48:54 UTC  
> Closed at: 2018-10-30 11:48:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/413  

Spirit were assuming that wchar_t is 32-bit and the content is UCS-4.  
It is wrong, the actual representation is implementation defined [lex.ccon]/6.  
However, on most Unix platforms this assumption is valid and gives the  
expected outcome, but on Windows wchar_t is 16-bit and the content is UTF-16.  
  
Fixes #395.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-10-28 22:46:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433749515  

wchar_t is implementation defined. If the code somehow assumes that, then it is a bug.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-10-28 22:57:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433750412  

What are alternatives? Remove `to_utf8` entirely? It is been here for 10 years and assumes that wchar_t is 32-bit UCS-4. I would be glad if you can fix this somehow without assuming any implementation details of wchar_t. Note that `std::codecvt` is deprecated in C++17 http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0618r0.html.

---

## Comment 3

> Username: djowel  
> Created_at: 2018-10-28 23:00:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433750664  

How about we support only a certain subset of types: those that we know work and is generally useful for typical platforms such as windows and unix? Assert the size required perhaps.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-10-28 23:18:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433751860  

> How about we support only a certain subset of types: those that we know work and is generally useful for typical platforms such as windows and unix?  
  
The problem is we cannot know what can be passed here, especially in C++98 because there is no char16_t and char32_t types.  
  
> Assert the size required perhaps.  
  
This function is only used in `parser.what()` (I have posted about this in https://github.com/boostorg/spirit/issues/395#issuecomment-433712373, please read it), no one noticed before that it does something strange with wchar_t on Windows because it is does not affect parsing in any way and its result is only seen in parser debugging with `BOOST_SPIRIT_QI_DEBUG`. I think it is not a good way to throw asserts from things that are cosmetic and a size assert will not help, possibly constexpr function could check what the actual wchar_t representation is, but again I do not think it is needed. Also I have added a test and when it will fail somewhere then things might be reconsidered.

---

## Comment 5

> Username: djowel  
> Created_at: 2018-10-28 23:34:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433753007  

I see. Well, I think we should simplify and just require exact representations to begin with. All platforms has some conversion support for it anyway. I think your last paragraph in your comment is the way to go.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-28 23:40:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433753415  

I am sorry I do not get you. If would be glad if you fix this if you know how it should be done the right way.

---

## Comment 7

> Username: djowel  
> Created_at: 2018-10-29 00:07:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433755348  

Sorry for the misunderstanding. To be clear, I think your solution here is OK.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-10-29 00:34:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/413#issuecomment-433757443  

I merge it then?

---
