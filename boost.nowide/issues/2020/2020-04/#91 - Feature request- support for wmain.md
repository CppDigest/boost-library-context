# #91 - Feature request: support for wmain [Closed]

> Username: tats-u  
> Created at: 2020-04-26 07:50:55 UTC  
> Updated at: 2020-04-26 10:31:28 UTC  
> Closed at: 2020-04-26 09:43:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/91  

It wold be nice if `(int, wchar_t **&)` overload were to be added to `boost::nowide::args` constructor.  
  
```c++  
#ifdef _WIN32  
int wmain(int argc, wchar_t **argv)  
{  
    boost::nowide::args _(argc, argv); // Must use an instance!  
    ...  
}  
#else  
int main(int argc, char **argv)  
{  
    boost::nowide::args _(argc, argv); // Must use an instance!  
    ...  
}  
#endif  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-04-26 09:09:30 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619514562  

What is your use case?  
  
The name is nowide, so you shouldn't need to use wide strings anywhere in your program but always use narrow, UTF-8 encoded strings. With the proposed wmain you'd have wide strings. So a) this is not wanted, as UTF-8 should be used everywhere and b) what do you expect `args` to do? The current, narrow `args` reads wide arguments from the system, converts them to UTF-8 and replaces argv by those.

---

## Comment 2

> Username: tats-u  
> Created at: 2020-04-26 09:43:25 UTC  
> Updated at: 2020-04-26 09:59:40 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619519202  

It seems that I don't have to use `wmain` by the help of `CommandLineToArgvW` in `args.hpp`.  
Is it true `args.fix_args` and `args.fix_env` temporarily replace legacy-encoded (ISO-8859-1, Shift-JIS, GBK, and so on) ones passed by `main` with UTF-8-encoded ones converted from the result of `CommandLineToArgvW` using `stackstring` ?  
Sorry for my misunderstanding and bothering you.  I understood the architecture of this and think I can use without hesitation.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-04-26 09:57:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619520953  

Yes, the whole library is designed so you never have to use `wchar_t` anywhere. If anything in the documentation is unclear please suggest a better wording. Documentation written by users for users is better than one from developers only :)

---

## Comment 4

> Username: tats-u  
> Created at: 2020-04-26 10:05:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619522099  

It may be difficult to understand what `boost::nowide::args` do with `argc` and `argv` without reading source.  It would be best if *I* can send a PR...  I'll try.

---

## Comment 5

> Username: Flamefire  
> Created at: 2020-04-26 10:14:37 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619523338  

Please do 👍   
  
Have you looked at the documentation of that class: https://www.boost.org/doc/libs/master/libs/nowide/doc/html/classboost_1_1nowide_1_1args.html IMO it has everything already. May you didn't find it? Then a link from the main page might be enough already.

---

## Comment 6

> Username: tats-u  
> Created at: 2020-04-26 10:25:55 UTC  
> Updated at: 2020-04-26 10:31:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/91#issuecomment-619524820  

I'd like a implication that the class TEMPORARILY COVERS the original `argv` (, `env`, and `argc`) with UTF-8 one(s) only while an instance of it alives.  I am trying to improve `docs/main.dox` first.
