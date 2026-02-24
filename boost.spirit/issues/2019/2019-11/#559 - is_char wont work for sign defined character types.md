# #559 - is_char wont work for sign defined character types [Closed]

> Username: rezaebrh  
> Created at: 2019-11-30 12:52:47 UTC  
> Updated at: 2019-12-04 04:46:30 UTC  
> Closed at: 2019-12-04 04:46:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/559  

`is_char<char>::value` is true  
`is_char<unsigned char>::value` is false  
`is_char<signed char>::value` is even false  
and  
`is_char<typename std::make_signed<unsigned char>::type>::value` is also false  
  
I need to discriminate character types from other  integral types. What should I do?

---

## Comment 1

> Username: djowel  
> Created at: 2019-11-30 22:56:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/559#issuecomment-560027884  

Why exactly do you want to discriminate character types? Do you have a use-case on how it affects spirit? Spirit's is_char only supports char and wchar_t. If you can show a use-case where this is problematic, then we can probably add more. Otherwise, you should just probably make your own traits for that.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-11-30 22:56:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/559#issuecomment-560027922  

What are you actually trying to achieve? The trait is not meant to be used by users. Its main purpose is a customization point for a case when you are not satisfied with defaults ([read the documentation](https://www.boost.org/doc/libs/1_71_0/libs/spirit/doc/html/spirit/advanced/customize.html)). IIUC, the `is_char` is responsible for enabling literal character/string parser creation/conversion from a literal char/string (like `lit('a')`/`lit("a")`).

---

## Comment 3

> Username: djowel  
> Created at: 2019-11-30 23:05:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/559#issuecomment-560028374  

> IIUC, the `is_char` is responsible for enabling literal character/string parser creation/conversion from a literal char/string (like `lit('a')`/`lit("a")`).  
  
That is correct.
