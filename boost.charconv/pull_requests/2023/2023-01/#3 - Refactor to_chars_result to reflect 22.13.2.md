# #3 Refactor to_chars_result to reflect 22.13.2 [Merged]

> Username: mborland  
> Created at: 2023-01-09 19:42:44 UTC  
> Updated at: 2023-01-09 21:08:13 UTC  
> Merged at: 2023-01-09 21:08:09 UTC  
> Closed at: 2023-01-09 21:08:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/3  

Uses `std::errc` instead of `int`. Also adds definition of `operator==` since defaulting the operator is not available until C++20.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-09 19:47:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376190888  

I used `int` to avoid including the entire `<system_error>` for just `std::errc`. It's a relatively heavy include because it pulls `<string>` and most people don't want that; for embedded uses, `<string>` might even be forbidden.

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-01-09 19:48:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/3#pullrequestreview-1241105638  

📁 include/boost/charconv/to_chars.hpp

```diff
   2 |+ // Copyright 2023 Matt Borland
   3 | // Distributed under the Boost Software License, Version 1.0.
   4 | // https://www.boost.org/LICENSE_1_0.txt
```

> Username: pdimov  
> Created_at: 2023-01-09 19:48:22 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#discussion_r1065044458  

I deliberately leave the copyright clause inside the `ifndef` to avoid it being scanned, although that's not particularly relevant in 2023.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-01-09 19:50:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/3#pullrequestreview-1241107759  

📁 include/boost/charconv/to_chars.hpp

```diff
   7 |+ #define BOOST_CHARCONV_TO_CHARS_HPP_INCLUDED
   8 |+ 
   9 |+ #include <system_error>
```

> Username: pdimov  
> Created_at: 2023-01-09 19:50:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#discussion_r1065045917  

As a rule, includes should be ordered such that more general includes follow less general ones. Including <system_error> before <boost/charconv/...> would hide a failure to include <system_error> in <boost/charconv/..> if that latter header uses something from <system_error>.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-01-09 19:50:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/3#pullrequestreview-1241107990  

📁 include/boost/charconv/to_chars.hpp

```diff
  22 | 
  21 |- BOOST_CHARCONV_DECL to_chars_result to_chars( char* first, char* last, int value, int base = 10 );
  23 |+ bool operator==(const to_chars_result& lhs, const to_chars_result& rhs)
```

> Username: pdimov  
> Created_at: 2023-01-09 19:50:15 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#discussion_r1065046051  

inline is missing here.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2023-01-09 19:51:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/3#pullrequestreview-1241109075  

📁 include/boost/charconv/to_chars.hpp

```diff
  23 |+ bool operator==(const to_chars_result& lhs, const to_chars_result& rhs)
  24 |+ {
  25 |+     if (lhs.ptr == rhs.ptr && lhs.ec == rhs.ec)
```

> Username: pdimov  
> Created_at: 2023-01-09 19:51:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#discussion_r1065046730  

Why not just `return lhs.ptr == rhs.ptr && lhs.ec == rhs.ec;` here?


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2023-01-09 19:52:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/3#pullrequestreview-1241110399  

📁 test/to_chars.cpp

```diff
  15 | 
  16 |-     BOOST_TEST_EQ( r.ec, 0 ) && BOOST_TEST_CSTR_EQ( buffer, "1048576" );
  16 |+     BOOST_TEST(!std::make_error_code(r.ec));
```

> Username: pdimov  
> Created_at: 2023-01-09 19:52:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#discussion_r1065047625  

Note that this changes the behavior of always executing the second test even if the previous one fails. This is probably OK here, as the final zero in `buffer` should always be present.


---

## Comment 7

> Username: mborland  
> Created_at: 2023-01-09 19:55:22 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376205909  

>   
  
That makes sense to me. Are we planning for embedded uses, and need to avoid throwing as well?

---

## Comment 8

> Username: pdimov  
> Created_at: 2023-01-09 19:56:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376208987  

In general, each new functionality should come with a test. In this case, we need a test for chars_format and its operator==.  
  
Also, I think that `== default` in C++20 also causes operator!= to be defined, so we need this and a test for it as well.

---

## Comment 9

> Username: pdimov  
> Created_at: 2023-01-09 19:59:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376216746  

> Are we planning for embedded uses, and need to avoid throwing as well?  
  
This functionality (directly or indirectly via Boost.JSON) is definitely useful for embedded, yes.  
  
It would definitely be good if these functions can be `noexcept`. I know that some corner floating-point cases require multiprecision arithmetic, so we might need to allocate and hence throw, but it would be good to avoid that in as many cases as possible.

---

## Comment 10

> Username: pdimov  
> Created_at: 2023-01-09 20:00:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376219786  

> Also, I think that `== default` in C++20 also causes operator!= to be defined  
  
Indeed it does, https://godbolt.org/z/Yj3YWTGzc.

---

## Comment 11

> Username: pdimov  
> Created_at: 2023-01-09 20:05:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376230554  

Wait, I used `chars_format` instead of `to_chars_result` by mistake. It's https://godbolt.org/z/zx5PGnvro, and the op!= is synthesized by C++20.

---

## Comment 12

> Username: pdimov  
> Created_at: 2023-01-09 20:12:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/3#issuecomment-1376251062  

A general comment on the inline friend in the standard: the function is declared as a `friend` inside the struct definition, even though all the members are public and therefore there's no need for it being a friend at all, to denote an inline friend. That is, a function whose definition is inline in the friend declaration.  
  
This is a special kind of function called "inline friend" or "hidden friend" because it's not visible to normal lookup, and therefore does not contribute to the enormous overload sets that are usually formed when code does `x == y` where one of `x` or `y` is in namespace `std`. A hidden friend only participates in overload resolution when the class is mentioned.  
  
TL;DR:  
  
```  
struct to_chars_result  
{  
    char* ptr;  
    int ec;  
  
    friend bool operator==(const to_chars_result& r1, const to_chars_result& r2)  
    {  
        return r1.ptr == r2.ptr && r1.ec == r2.ec;  
    }  
};  
```

---
