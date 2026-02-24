# #33 - Please add support for std::basic_string_view [Closed]

> Username: hia3  
> Created at: 2017-11-16 01:11:41 UTC  
> Updated at: 2021-01-11 15:09:02 UTC  
> Closed at: 2021-01-11 15:09:02 UTC  
> Url: https://github.com/boostorg/nowide/issues/33  

It would be nice to have overloads of `widen`/`narrow` for `(w/u16/u32)string_view`, if possible.

---

## Comment 1

> Username: Frkymhn  
> Created at: 2020-01-09 12:35:41 UTC  
> Url: https://github.com/boostorg/nowide/issues/33#issuecomment-572542617  

i want to work on this issues.please guide me

---

## Comment 2

> Username: Flamefire  
> Created at: 2020-01-09 14:26:08 UTC  
> Url: https://github.com/boostorg/nowide/issues/33#issuecomment-572584370  

I'm going to clean up the header a bit and move the `basic_convert` function into detail, see https://github.com/boostorg/nowide/pull/61. Then it should be pretty easy: Instead of a (w)string take a (w)string_view and pass it on, just as the string function do. Obviously you'll need a check if string_view is supported. Look for a appropriate boost macro.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-01-09 14:28:24 UTC  
> Url: https://github.com/boostorg/nowide/issues/33#issuecomment-572585394  

as for (u16/u32)string_view: You can take a template in widen and enable_if on the char type. I guess a trait `is_wide_char`  specialized for w/u16/u32 char would be appropriate

---

## Comment 4

> Username: Flamefire  
> Created at: 2020-10-05 16:23:31 UTC  
> Url: https://github.com/boostorg/nowide/issues/33#issuecomment-703740057  

@hia3 I added a PR (#117) which makes those functions (and the `convert_string` template) accept string_view instances.  
  
Let me know if that solves your problem
