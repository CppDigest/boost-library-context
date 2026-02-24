# #143 - Feature Request: Add C++14 constexpr Support to boost::optional [Closed]

> Username: alandefreitas  
> Created at: 2026-01-29 23:22:53 UTC  
> Updated at: 2026-02-20 05:36:02 UTC  
> Closed at: 2026-02-17 16:17:18 UTC  
> Url: https://github.com/boostorg/optional/issues/143  

Currently, `boost::optional` cannot be used in constexpr contexts in C++14/17 because it lacks constexpr constructors. This limits its usefulness in constexpr-heavy code like compile-time parsers and validators.  
  
```cpp  
constexpr boost::optional<int> g()  
{  
    return 5;  
}  
  
constexpr int y = g().value();  
```  
  
```  
error: constexpr function's return type 'boost::optional<int>' is not a literal type  
note: 'optional<int>' is not literal because it is not an aggregate and has   
      no constexpr constructors other than copy or move constructors  
```  
  
https://godbolt.org/z/P667adWE7  
  
**Use Case**: We're working on adding constexpr support to Boost.URL to enable compile-time URL parsing and validation. Many of the parsing rules naturally return optional values (e.g., optional userinfo, optional port), but we can't use `boost::optional` in constexpr contexts.  
  
For comparison, `boost::system::result` already works perfectly in constexpr contexts:  
  
```cpp  
constexpr boost::system::result<int> f()  
{  
    return 5;  
}  
  
constexpr int x = f().value(); // Works fine!  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2026-01-31 10:06:30 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3828075656  

Thank you. I'll add it.  
  
Just to satisfy my curiosity, why did you decide to go with `boost::optional` when you have `std::optional` handy in C++14?  
  
(I am doing my own survey on if and why people would prefer `boost::optional` over `std::optional`.)

---

## Comment 2

> Username: akrzemi1  
> Created at: 2026-01-31 23:41:50 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3829632470  

There is one issue, though. In order to implement it correctly, I need `std::addressof` and it is not `constexpr` until C++17.  
I guess I can make it `constexpr` for types that do not overload unary operator `&`. Will that work?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-01 12:43:11 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3830995731  

> Just to satisfy my curiosity, why did you decide to go with boost::optional when you have std::optional handy in C++14?  
  
Ok, you haven't. `std::optional` is C++17.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2026-02-03 02:44:10 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3838702273  

> Ok, you haven't. std::optional is C++17.  
  
Yes 🙂🙃  
  
> I guess I can make it constexpr for types that do not overload unary operator &  
  
Definitely.  
  
I think my message was also a little bit confusing because we're trying to implement those compile-time parsing rules in C++14, but the library is still C++11. 😁

---

## Comment 5

> Username: akrzemi1  
> Created at: 2026-02-07 21:50:36 UTC  
> Updated at: 2026-02-07 21:51:21 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865562686  

Ok, it turns out that there is a limit on what I can do prior to C++17's "mandatory copy elision".  
I can make all constructors for which I can statically determine whether they end up containing a value or not:  
 * `optional()`  (will not contain value)  
 * `optional(none_t)` (will not contain value)  
 * `optional(in_place_init_t, ...)` (will contain value)  
 * `optional(T const&)` (will contain value)  
  
But I cannot do it for:  
* copy constructor  
* move constructor  
* `optional(bool, T const&)`  
* `optional(in_place_init_if_t, ...)`  
  
I can see that Boost.Variant2 suffers the same constraints: https://godbolt.org/z/aY41TYaYE.  
  
@alandefreitas,  will such partial implementation work for you?  
Here's a C++11 implementation I did a while back: https://github.com/akrzemi1/Optional/.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2026-02-07 21:58:24 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865584730  

Alternatively, I could provide a trait for `boost::optional` wherewith a programmer commits that their type's default constructor is allowed to be used in `optional`'s defaull constructor, and with this trait and the type being trivially copyable, I can make such specializations `constexpr`. But I really don't like it.

---

## Comment 7

> Username: alandefreitas  
> Created at: 2026-02-07 23:12:08 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865726644  

> will such partial implementation work for you?  
  
I'm 90% sure it would. Especially if variant2 has the same constraint. 🙂  
  
I can also enable more things in boost.url depending on the standard. 🚀  
  
> statically determine whether they end up containing a value or not  
  
mmm... Interesting. Why is that?

---

## Comment 8

> Username: akrzemi1  
> Created at: 2026-02-07 23:34:43 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865751354  

The only way to implement constexpr `optional` -- prior to all the constexpr allocation and manula lifetime management -- is using a union. Since C++11 unions allow arbitrary (non-trivial) types, but you have to define special member functions yourself and tell what you want them to do. So, I have a set of constructors that I trigger by tag types, to initialize what member I want and with what I want. Such initialization works with `constexpr`, but I then cannot change the active member of a union in a core constant expression. Prior to C++17 there is no way to conditionally trigger a different constructor based on runtime input. In C++17 we have mandatory copy elision, so I can create a factory function with as many branches as I desire, and when I initialize there are no moves or copies.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2026-02-07 23:46:57 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865767738  

It makes sense... Thanks for taking the time to explain.   
  
Yes, a partial constexpr implementation like that would be good enough for us. For our use case, being able to construct an engaged or disengaged optional at the point of return is what really matters, and we can structure the code to avoid the cases that aren’t possible pre-C++17.  
  
From our side, this would already be a big improvement, and we can enable more as needed, depending on the language version.

---

## Comment 10

> Username: akrzemi1  
> Created at: 2026-02-07 23:58:47 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865784241  

Thanks. Working on it.  
BTW, just checked that `boost::system::result` suffers from the same problem (as it uses Variant2 in implementation):  
https://godbolt.org/z/Y57cvsY5q.

---

## Comment 11

> Username: alandefreitas  
> Created at: 2026-02-08 00:16:02 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3865806944  

Yes. That's actually good news, because boost::system::result has already been working in the cases where we needed it so far. 😅

---

## Comment 12

> Username: akrzemi1  
> Created at: 2026-02-08 10:41:35 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3866984943  

The "`constexpr`" implementation is now available in branch `constexpr`. Are you able to test it from there?

---

## Comment 13

> Username: alandefreitas  
> Created at: 2026-02-08 16:58:37 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3867554619  

Amazing... I'll test it out. This is going to take a while because this constexpr feature in Boost.URL is about ~5k LOC. But I'll get back to you. :)

---

## Comment 14

> Username: akrzemi1  
> Created at: 2026-02-08 17:10:14 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3867605655  

Actually, better test branch `feature/constexpr`. I am applying all the fixes there.

---

## Comment 15

> Username: alandefreitas  
> Created at: 2026-02-08 20:11:03 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3868174276  

I just tried. It seems like boost::optional is great, but I got blocked by another issue with boost::result. I couldn't constexpr construct it from a custom error code. Let's see what Peter says. :)  
  
https://github.com/boostorg/url/issues/890#issuecomment-3868153507

---

## Comment 16

> Username: akrzemi1  
> Created at: 2026-02-17 14:03:57 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3914876855  

This is now delivered to `develop`.

---

## Comment 17

> Username: alandefreitas  
> Created at: 2026-02-17 16:17:18 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3915645058  

Amazing! 🥳 I'll move from feature/constexpr to develop locally. Thanks! :)

---

## Comment 18

> Username: alandefreitas  
> Created at: 2026-02-20 02:45:20 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3931287018  

Hi. I also merged the changes to develop in boost.url but don't know when to merge to master. Are your changes in master yet?

---

## Comment 19

> Username: akrzemi1  
> Created at: 2026-02-20 05:36:02 UTC  
> Url: https://github.com/boostorg/optional/issues/143#issuecomment-3931776311  

Yes. This is already in master:  
https://github.com/boostorg/optional/commit/d46b9c3f773b537a22768b7b3547e1d24d957401
