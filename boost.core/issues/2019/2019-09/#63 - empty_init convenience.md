# #63 - empty_init convenience [Closed]

> Username: breese  
> Created at: 2019-09-28 11:53:36 UTC  
> Updated at: 2019-11-24 02:01:00 UTC  
> Closed at: 2019-11-24 02:01:00 UTC  
> Url: https://github.com/boostorg/core/issues/63  

Instantiating `boost::empty_value` with arguments is done using the `empty_init_t` tag. This means that we have to write something like `base(empty_init_t{}, args...`.  
  
Can we add an `empty_init` instance of that tag, so we can write `base(empty_init, args...)`?  
  
We can avoid the usual problems of header-only instantiations of a global object by changing `struct empty_init_t {};` into `enum empty_init_t { empty_init };`.

---

## Comment 1

> Username: glenfe  
> Created at: 2019-10-01 12:09:36 UTC  
> Updated at: 2019-10-01 12:10:00 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537006353  

A `BOOST_STATIC_CONSTEXPR empty_init_t empty_init = empty_init_t();` should satisfy that request too, right?

---

## Comment 2

> Username: breese  
> Created at: 2019-10-02 09:48:14 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537420420  

That will work.  
  
The reason I mention the enum alternative, is that when I had to investigate the binary size of a large embedded project, I found a `boost::none` (from Boost.Optional) instance in each translation unit that used this constant. While this is not a major problem per se, the enum solution completely avoids it, which is why I prefer enum for tagging.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-10-02 10:35:37 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537435300  

I suppose, we should mark `boost::none` `inline` on C++17 compilers. That should mitigate binary size bloat.

---

## Comment 4

> Username: glenfe  
> Created at: 2019-10-02 10:50:44 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537439694  

It's also why I never created an empty_init constant initially. The four extra characters "_t()" did not seem like.a big deal. :)  
  
Users will always create constants if they want them, maybe even shorter names than the ones we might provide.  
  
(We could even just create a type alias for `empty_init_t` to a shorter type name.)

---

## Comment 5

> Username: Lastique  
> Created at: 2019-10-02 10:57:45 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537441596  

I think the mental pattern to assign a global value (think `nullopt`, `nullptr`, `nothrow`) is too strong. And asking users to create constants isn't solving anything - they will still have the binary bloat. It only shifts the burden on the users.

---

## Comment 6

> Username: breese  
> Created at: 2019-10-02 11:18:53 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537447553  

There is an [issue](https://github.com/boostorg/optional/issues/33) for `boost::none`.  
  
C++17 inline constant is good general solution, but for integers or tags the enum solution works with older compilers as well.

---

## Comment 7

> Username: Lastique  
> Created at: 2019-10-02 11:28:20 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537450203  

Enum, if we're talking about unscoped enums, has the potential to be converted to `int`, which can bite if a function overload accepts `int` and a type constructible from the enum. I believe, the compiler should pick the `int` overload because this is a builtin conversion as opposed to a user-defined constructor.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-10-02 11:40:07 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537453533  

`constexpr` is automatically `inline`, but `none` isn't `BOOST_STATIC_CONSTEXPR`.

---

## Comment 9

> Username: pdimov  
> Created at: 2019-10-02 11:45:50 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537455185  

Wait, no, this was only true for static class members. Namespace-scope `constexpr` variables aren't automatically `inline`. Sad.  
  
Maybe we need to add `inline` to `BOOST_STATIC_CONSTEXPR`.

---

## Comment 10

> Username: glenfe  
> Created at: 2019-10-02 12:03:24 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537460936  

I'm not a fan of the `enum` approach; I prefer keeping `empty_init_t` a mono state type (like `nullopt_t`, `nothrow_t`, `inplace_t`). Maybe a new Boost.Config macro, with this intention in mind. e.g. `BOOST_INLINE_CONSTEXPR`  which is `inline constexpr` if available, `static constexpr` if not.

---

## Comment 11

> Username: Lastique  
> Created at: 2019-10-02 14:03:44 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537507421  

Honestly, I don't see the point of `BOOST_STATIC_CONSTEXPR` (or `BOOST_INLINE_CONSTEXPR`) given that we already have `BOOST_CONSTEXPR_OR_CONST`. It might be useful to add `BOOST_INLINE_VARIABLE`, which expands to `inline` or nothing depending on `BOOST_NO_CXX17_INLINE_VARIABLES`.

---

## Comment 12

> Username: glenfe  
> Created at: 2019-10-02 14:33:41 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537521671  

Sounds fine to me.  So in this case, usage would be of the form: `BOOST_INLINE_VARIABLE BOOST_CONSTEXPR_OR_CONST type name = type();` ?

---

## Comment 13

> Username: Lastique  
> Created at: 2019-10-02 14:35:34 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537522632  

Yes.

---

## Comment 14

> Username: glenfe  
> Created at: 2019-10-02 14:37:38 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537523666  

And in the case where `BOOST_NO_CXX17_INLINE_VARIABLES` is defined, we're fine with the above too? i.e. We don't want that to have `static` or be in an unnamed namespace?

---

## Comment 15

> Username: Lastique  
> Created at: 2019-10-02 14:39:41 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537524661  

Yes, we're fine. Namespace-scope constants have internal linkage by default.

---

## Comment 16

> Username: glenfe  
> Created at: 2019-10-02 14:47:02 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537528135  

@pdimov Do you remember why bind couldn't have the placeholder constants in GCC versions before 4?  
```  
#if defined(__BORLANDC__) || defined(__GNUC__) && (__GNUC__ < 4)  
```

---

## Comment 17

> Username: pdimov  
> Created at: 2019-10-02 15:23:50 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537545008  

`BOOST_INLINE_VARIABLE BOOST_CONSTEXPR_OR_CONST` is missing the current `static` though. Although I'm not sure whether it's needed. Not sure why this is any improvement over `BOOST_INLINE_CONSTEXPR` that expands to whatever's correct.

---

## Comment 18

> Username: pdimov  
> Created at: 2019-10-02 15:24:08 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537545157  

No, I don't.

---

## Comment 19

> Username: pdimov  
> Created at: 2019-10-02 15:55:46 UTC  
> Url: https://github.com/boostorg/core/issues/63#issuecomment-537559759  

Looking at the history, it was probably a precompiled header problem.
