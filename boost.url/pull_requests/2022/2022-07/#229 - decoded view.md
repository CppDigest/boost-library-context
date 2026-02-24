# #229 decoded view [Merged]

> Username: alandefreitas  
> Created at: 2022-07-12 23:04:51 UTC  
> Updated at: 2022-08-03 21:07:09 UTC  
> Merged at: 2022-07-16 01:30:20 UTC  
> Closed at: 2022-07-16 01:30:20 UTC  
> Url: https://github.com/boostorg/url/pull/229  

## Context (`const_string`)  
  
We have been seeing many foundational issues with `const_string`, including the recent issues #159 and #223. When compared to `std::string` or `std::string_view`, its design has been becoming more complex over time (#163, #164) and we are constantly exploring new solutions around it (#112, #127).  
  
Some of its problems can be improved by implementing it as a concrete type with the appropriate operators (#127). However, while recently exploring examples of use cases of URIs (#226, #224), we have identified problems that cannot be solved with these extra operators.   
  
For instance, routers almost never need to allocate memory and convert decoded values to `std::string`. `const_string` was forcing the application to perform ~2 or 3 allocations in a context where no allocations were really required, not to mention the lack of ergonomics in handling static_pools to avoid these allocations. Although `const_string` contains an inline buffer, the user still needs to handle these potential allocations.  
  
Another smaller problem is mentioned in the mailing list. A `url_view` has functions such as `url_view::XXXXXX()` and `url_view::encoded_XXXXXX()` for all URI components, where the default function `url_view::XXXXXX()` is the expensive one. Some people have found this counterintuitive. Other people have considered only using the functions for the encoded components, which could defy the purpose of the library to a large extent.  
  
## This PR (`decoded_view`)  
  
This PR introduces a "transform-view" type for percent decoded strings. Thus, all pairs of functions for URI components (`url_view::XXXXXX()` and `url_view::encoded_XXXXXX()`) would return cheap reference types. The user can use both functions without worrying about unnecessary memory allocations.   
  
In cases where memory allocation is required, the allocation is deferred until the user really needs it. The user has total control over how the memory will be allocated. It also avoids an extra allocation when `const_string` would be converted to `std::string` and avoids all allocations when the decoded string is only required for comparisons and checking conditions. When the user needs common conversions, such as `std::string`, helper functions such as `to_string()` are provided for new and recycled objects.  
  
The view type has the usual string operators, and should be almost as convenient as a `string_view`. The main difference is iterators are bidirectional, so `operator[]` is not available. One would need to advance the iterator or allocate memory. In practice, we don't need `operator[]` for many common use cases, like comparing segments or query values.   
  
A summary of the allowed operations:  
  
- Iterators: `begin`, `cbegin`, `end`, `cend`, `rbegin`, `crbegin`, `rend`, `crend`  
- Access: `front`, `back`, `encoded_data`  
- Capacity: `size`, `length`, `encoded_size`, `encoded_length`, `max_size`, `empty`  
- Operations: `copy`, `compare`, `to_string`, `append_to`  
- Conversions: explicit `std::basic_string`  
- Non-member: All operators, `oeprator<<(ostream, decoded_view)`  
  
About the operations:  
- Assignment can construct new objects or reuse recycled objects.   
- Appending can avoid temporary values.  
- Comparisons don't require any allocations.   
- Adding other operations that exist in `string_view` are possible with the same time complexity: `starts_with`, `ends_with`, `contains`, `find`, `find_*`   
- Adding other operations that exist in `string_view` are possible with the linear time complexity, which would still be faster than allocating memory: `substr`, `remove_prefix`, `remove_suffix`  
  
## Tests  
  
Besides testing all member functions, as usual, the tests include use cases people have brought up (`testConversion()` and `testAppend()`), snippets that came up in the examples (mostly in `testCompare()`), and use cases analogous to all examples in the Tony Table of PR #127 (`testPR127Cases()`).  
  
## Naming  
  
The initial name for this class is `decoded_view`.    
  
- The suffix `view` indicates it doesn't own the data. The suffix `range` could denote both views and containers, which makes the suffix `range` useless.  
  
- The prefix `decoded` is short and, in the context, is enough to convey that the string is percent decoded. So this avoids very long unergonomic names such as `percent_decoded_range` or even `pct_decoded_range`.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-07-12 23:05:41 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1182582474  

@akrzemi1, would you like to chip in?

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-12 23:17:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036581502  

📁 include/boost/url/decoded_view.hpp

```diff
  94 |+     BOOST_CONSTEXPR
  95 |+     decoded_view(
  96 |+         bool plus_to_space = true) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-12 23:17:20 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919506129  

I think this should accept the entire options struct and not just a naked bool


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-12 23:57:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036601971  

📁 include/boost/url/decoded_view.hpp

```diff
 108 |+         : p_(str)
 109 |+         , n_(traits_type::length(str))
 110 |+         , dn_(pct_decode_bytes_unchecked(string_view(str)))
```

> Username: vinniefalco  
> Created_at: 2022-07-12 23:57:58 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919522208  

User inputs need to be validated, we should not used the unchecked algorithm here


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-12 23:59:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036602640  

📁 include/boost/url/decoded_view.hpp

```diff
 142 |+         bool plus_to_space = true) noexcept
 143 |+ #endif
 144 |+         : p_(first)
```

> Username: vinniefalco  
> Created_at: 2022-07-12 23:59:28 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919522770  

Do we need this overload? Doesn't construction from `string_view` cover everything?

> Username: vinniefalco  
> Created_at: 2022-07-14 14:48:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921245036  

No answer?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-12 23:59:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036602828  

📁 include/boost/url/decoded_view.hpp

```diff
 164 |+     /** Constructor
 165 |+      */
 166 |+     decoded_view(
```

> Username: vinniefalco  
> Created_at: 2022-07-12 23:59:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919522907  

Is this overload needed?


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:00:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036603161  

📁 include/boost/url/decoded_view.hpp

```diff
 178 |+         Returns an iterator to the first decoded character of the string
 179 |+ 
 180 |+         @return const_iterator to the first character
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:00:36 UTC  
> Updated_at: 2022-07-13 00:00:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919523168  

The "@return" clause is redundant here. How many times do we need to tell the user that this function returns an iterator? "C'mon man!"


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:01:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036603423  

📁 include/boost/url/decoded_view.hpp

```diff
 187 |+     BOOST_URL_DECL
 188 |+     const_iterator
 189 |+     cbegin() const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:01:10 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919523404  

too much try-hard we dont need this


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:02:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036603933  

📁 include/boost/url/decoded_view.hpp

```diff
 249 |+     BOOST_CONSTEXPR
 250 |+     const char*
 251 |+     encoded_data() const
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:02:16 UTC  
> Updated_at: 2022-07-13 00:02:17 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919523796  

Why are we returning a pointer here instead of the string view?

> Username: vinniefalco  
> Created_at: 2022-07-13 00:27:57 UTC  
> Updated_at: 2022-07-13 00:28:23 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919541041  

This could just be  
```  
string_view encoded() const noexcept;  
```  
or  
```  
string_view encoded_str() const noexcept;  
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:02:47 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919650595  

If you're going to resolve it please explain the rationale

> Username: alandefreitas  
> Created_at: 2022-07-13 21:02:13 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920511140  

I did exactly what I've been told but I haven't pushed it yet.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036604333  

📁 include/boost/url/decoded_view.hpp

```diff
 256 |+     /** Returns the number of char elements in the string
 257 |+ 
 258 |+         @return The number of char elements in the string
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919524101  

Bro..


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036604412  

📁 include/boost/url/decoded_view.hpp

```diff
 268 |+     BOOST_CONSTEXPR
 269 |+     size_type
 270 |+     length() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:15 UTC  
> Updated_at: 2022-07-13 00:03:16 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919524175  

Why two versions of the same function?

> Username: alandefreitas  
> Created_at: 2022-07-13 01:56:35 UTC  
> Updated_at: 2022-07-13 01:56:36 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919573222  

I'm modeling the class after `string_view`. At first, it's kind of the point that replacing `string_view` with a `decoded_view` should be as easy as possible.

> Username: vinniefalco  
> Created_at: 2022-07-13 05:05:59 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919651860  

No one uses `string_view::length` We should not repeat the mistakes made by other authors. Prefer typing less rather than more. "You ain't gonna need it." Let users complain first, that `decoded_view` is hard to drop-in as a replacement for `string_view`, before adding the kitchen sink. Use a light touch, just add what is urgent and leave the rest for another day. No one is using reverse iterators bro! Except maybe for college students and people trying to get rich writing C++ books.  
  
Look:  
  
![image](https://user-images.githubusercontent.com/1503976/178654883-993c0bc0-964d-46e1-ba79-544d5181a59b.png)  
  
![image](https://user-images.githubusercontent.com/1503976/178654913-1404e394-794f-4265-a3b8-13f2555f7bf2.png)


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036604641  

📁 include/boost/url/decoded_view.hpp

```diff
 198 |+         @return const_iterator to the character following the last character
 199 |+     */
 200 |+     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:03:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919524349  

This should probably be inline


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:04:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036605067  

📁 include/boost/url/decoded_view.hpp

```diff
 287 |+     BOOST_CONSTEXPR
 288 |+     size_type
 289 |+     encoded_length() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:04:41 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919524735  

Too much try-hard here why are there so many functions which do the same thing, a lot of repetition, a lot of busy work, a lot of boilerplate, a lot of.. there's just TOO MANY NOTES  
https://www.youtube.com/watch?v=dCud8H7z7vU

> Username: alandefreitas  
> Created_at: 2022-07-13 01:59:19 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919574255  

I'm Mozart?

> Username: vinniefalco  
> Created_at: 2022-07-13 04:19:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919632800  

Sure :)


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036605539  

📁 include/boost/url/decoded_view.hpp

```diff
 302 |+     BOOST_CONSTEXPR
 303 |+     size_type
 304 |+     max_size() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:05:45 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919525057  

what's the point of this


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:06:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036605774  

📁 include/boost/url/decoded_view.hpp

```diff
 307 |+     }
 308 |+ 
 309 |+     /** Checks whether the string is empty
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:06:16 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919525252  

The javadoc brief for functions that return information should start with the word "Return"


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:06:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036605976  

📁 include/boost/url/decoded_view.hpp

```diff
 312 |+         i.e. whether `size() == 0`.
 313 |+ 
 314 |+         @return `true` if the string is empty
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:06:43 UTC  
> Updated_at: 2022-07-13 00:06:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919525429  

You don't always need this @return clause, especially if you have already explained it once, twice, three times, etc


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:08:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036606933  

📁 include/boost/url/decoded_view.hpp

```diff
 400 |+     template <class A>
 401 |+     std::basic_string<char, std::char_traits<char>, A>&
 402 |+     to_string(std::basic_string<char, std::char_traits<char>, A> & str) const
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:08:49 UTC  
> Updated_at: 2022-07-13 00:08:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919526150  

we said we are going to do away with 1-arg `to_string` and just have `std::string to_string() const;`

> Username: alandefreitas  
> Created_at: 2022-07-13 02:00:36 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919574809  

No recycling?

> Username: vinniefalco  
> Created_at: 2022-07-13 04:19:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919633054  

`assign_to` and `append_to` can both be used on recycled things


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:09:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036607331  

📁 include/boost/url/decoded_view.hpp

```diff
 416 |+     template <class A = std::allocator<char>>
 417 |+     std::basic_string<char, std::char_traits<char>, A>
 418 |+     to_string() const
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:09:42 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919526458  

No no no... too much try-hard here! Users don't want to have to write `x.template to_string<Y>()` this should just return `std::string`

> Username: alandefreitas  
> Created_at: 2022-07-13 02:02:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919582009  

They don't have to write `x.template to_string<Y>()`.  
  
https://github.com/alandefreitas/url/blob/9c576efadbc653faef31a7f827a5182d462fd488/test/unit/decoded_view.cpp#L331

> Username: vinniefalco  
> Created_at: 2022-07-13 04:21:22 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919633513  

Sometimes you do but in any event the signature should be just `std::string decoded_view::to_string() const` and it can be inline (calling assign_to on a returned local)

> Username: vinniefalco  
> Created_at: 2022-07-13 05:12:07 UTC  
> Updated_at: 2022-07-13 05:12:25 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919654421  

The general rule is that we try to avoid creating APIs where the user passes a template parameter to a member function (think `std::get`). Sometimes it is OK but it is rare, this is not one of those rare cases.  
  
https://godbolt.org/z/7sT4T487P  
  
```  
#include <type_traits>  
  
struct T  
{  
    template<class U>  
    void to_string() {}  
};  
  
void f1(T& t)  
{  
    t.to_string<int>();  
}  
  
template<  
    class U,  
    typename std::enable_if<  
        std::is_same<U, T>::value>::type* = nullptr>  
void f2(U& t)  
{  
    t.to_string<int>();  
}  
  
int main()  
{  
    T t;  
    f1(t);  
    f2(t);  
}  
```


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:10:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036607687  

📁 include/boost/url/decoded_view.hpp

```diff
 445 |+     template <class A>
 446 |+     std::basic_string<char, std::char_traits<char>, A>&
 447 |+     append_to(std::basic_string<char, std::char_traits<char>, A> & str) const;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:10:29 UTC  
> Updated_at: 2022-07-13 00:10:30 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919526752  

I thought we were accepting any object that has a member `append` that takes two iterators and has a `char` value type? like `json::string`?

> Username: alandefreitas  
> Created_at: 2022-07-13 02:05:04 UTC  
> Updated_at: 2022-07-13 02:05:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919584554  

Yes. We can.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:11:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036607991  

📁 include/boost/url/decoded_view.hpp

```diff
 447 |+     append_to(std::basic_string<char, std::char_traits<char>, A> & str) const;
 448 |+ 
 449 |+     /** Compares two decoded strings
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:11:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919527060  

"Return true if two strings are equal after percent decoding"


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:11:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036608113  

📁 include/boost/url/decoded_view.hpp

```diff
 468 |+     operator==(
 469 |+         decoded_view lhs,
 470 |+                 string_view rhs ) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:11:29 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919527160  

goofy indent


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:20:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036612276  

📁 include/boost/url/decoded_view.hpp

```diff
  34 |+ 
  35 |+     The correctness of the underlying string
  36 |+     is assumed. The values returned by
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:20:16 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919530286  

grammar


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:20:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036612562  

📁 include/boost/url/decoded_view.hpp

```diff
  31 |+ 
  32 |+     These views are references to the encoded
  33 |+     strings that allow cheap copies.
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:20:50 UTC  
> Updated_at: 2022-07-13 00:20:51 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919530509  

We don't use the word "cheap" in formal exposition. Prefer "inexpensive"


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:21:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036612665  

📁 include/boost/url/decoded_view.hpp

```diff
  23 |+ namespace urls {
  24 |+ 
  25 |+ /** A view on a string as percent decoded characters
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:21:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919530562  

Needs work

> Username: alandefreitas  
> Created_at: 2022-07-13 21:25:01 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920526624  

I've improved this (locally for now) but I'll let you let me know when we can resolve this.


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:21:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036612990  

📁 include/boost/url/decoded_view.hpp

```diff
  84 |+ 
  85 |+     /// @copydoc const_iterator
  86 |+     using iterator = const_iterator;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:21:47 UTC  
> Updated_at: 2022-07-13 00:22:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919530797  

Prefer typing less rather than more. `iterator` should be the class and `const_iterator` should be the alias. Fewer characters.

> Username: alandefreitas  
> Created_at: 2022-07-13 02:09:59 UTC  
> Updated_at: 2022-07-13 02:10:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919586440  

`const_iterator` looked better here because it _is_ a const-iterator. `boost::core::string_view` also does exactly that. Self-documenting the iterator makes it easy for someone reading the code to understand what's happening.

> Username: vinniefalco  
> Created_at: 2022-07-13 04:22:30 UTC  
> Updated_at: 2022-07-13 04:22:31 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919633912  

no one cares about that or will even look at it, nothing is becoming "easier" it is just making things more verbose. they are both the same type...


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:23:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036618526  

📁 include/boost/url/decoded_view.hpp

```diff
 217 |+     BOOST_URL_DECL
 218 |+     const_reference
 219 |+     front() const;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:23:11 UTC  
> Updated_at: 2022-07-13 00:23:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919535369  

This should probably be inline


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:26:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036625661  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  56 |+ decoded_view::const_reference
  57 |+ decoded_view::
  58 |+ back() const
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:26:47 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919540617  

can't we simply inline this   
```  
BOOST_ASSERT(! empty());  
return *(end()--);  
```


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:26:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036625757  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  51 |+ {
  52 |+     BOOST_ASSERT( !empty() );
  53 |+     return *begin();
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:26:59 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919540714  

Can't we simply inline this


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:28:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036626495  

📁 include/boost/url/decoded_view.hpp

```diff
 279 |+     BOOST_CONSTEXPR
 280 |+     size_type
 281 |+     encoded_size() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:28:46 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919541299  

this is the same as `encoded_str().size()`


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036626632  

📁 include/boost/url/decoded_view.hpp

```diff
 321 |+     }
 322 |+ 
 323 |+     /** Copies characters
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919541414  

Needs work

> Username: alandefreitas  
> Created_at: 2022-07-13 21:27:46 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920528427  

I've improved this (locally for now) but I'll let you let me know when we can resolve this.


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036626768  

📁 include/boost/url/decoded_view.hpp

```diff
 331 |+         Exceptions thrown on invalid positions.
 332 |+ 
 333 |+         @throw out_of_range If `pos > size()`
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:21 UTC  
> Updated_at: 2022-07-13 00:29:22 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919541511  

I don't think this renders correctly


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036626950  

📁 include/boost/url/decoded_view.hpp

```diff
 362 |+     BOOST_URL_DECL
 363 |+     int
 364 |+     compare(string_view other) const;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 00:29:42 UTC  
> Updated_at: 2022-07-13 00:29:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919541663  

shouldn't this be noexcept?


---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2022-07-13 02:55:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1182707023  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:24:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036745203  

📁 include/boost/url/decoded_view.hpp

```diff
 150 |+      */
 151 |+     decoded_view(
 152 |+         urls::string_view str,
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:24:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919634616  

do you need the namespace qualifier here


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:27:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036746661  

📁 include/boost/url/impl/decoded_view.hpp

```diff
   9 |+ 
  10 |+ #ifndef BOOST_URL_IMPL_PCT_DECODED_RANGE_HPP
  11 |+ #define BOOST_URL_IMPL_PCT_DECODED_RANGE_HPP
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:27:39 UTC  
> Updated_at: 2022-07-13 04:27:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919635799  

the macro doesn't match the file name


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:29:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036747409  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  79 |+     {
  80 |+         BOOST_ASSERT(pos_ != nullptr);
  81 |+         pos_ += *pos_ == '%' ? 3 : 1;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:29:19 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919636372  

coverage...just write it out the long way`


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:29:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036747627  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  65 |+ 
  66 |+     reference
  67 |+     operator*() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:29:46 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919636535  

This should probably not be inline, and at some point we probably want to just decode it ourselves instead of calling an entire function

> Username: alandefreitas  
> Created_at: 2022-07-13 21:34:33 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920533655  

I'm not sure I understand why it shouldn't be inline and why we would duplicate this code

> Username: alandefreitas  
> Created_at: 2022-07-13 21:35:41 UTC  
> Updated_at: 2022-07-13 21:36:06 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920534316  

P.S.: I'm literally "not sure I understand". This is not a euphemism to say I disagree.

> Username: vinniefalco  
> Created_at: 2022-07-14 03:30:45 UTC  
> Updated_at: 2022-07-14 03:30:57 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920718752  

We want it out of line because it will be a little too big after you manually decode it. We should not call `pct_decode_unchecked` just to decode one character because the function pays a high up-front cost just to be invoked, as it sets up things for decoding an entire buffer not one character. And it checks end_ which is not needed here. Maybe writing this implementation detail would help:  
  
```  
inline  
char  
pct_decode_one_unchecked(  
    char const*& src,  
    bool plus_to_space) noexcept;  
```  
  
and we could express the existing functions in terms of this? Anyway this is an optimization which is not important now (but do make a note of it, perhaps in an issue). What is important is that we put it out-of-line.


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:30:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036748194  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  88 |+         BOOST_ASSERT(pos_ != begin_);
  89 |+         if (pos_ - begin_ >= 3 &&
  90 |+             *(pos_ - 3) == '%')
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:30:52 UTC  
> Updated_at: 2022-07-13 04:32:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919636970  

This is a weird way to write `pos_[-3]`...  
  
Since msvc has no "likely" intrinsic, the more likely case should be written first if you care about such things. I think non-escaped characters are more likely. Have fun inverting the logical expression.

> Username: alandefreitas  
> Created_at: 2022-07-13 21:39:07 UTC  
> Updated_at: 2022-07-13 21:39:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920536409  

Classic de morgan! :)


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:34:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036749810  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 121 |+         const_iterator const& other) const noexcept
 122 |+     {
 123 |+         return pos_ == other.pos_;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:34:13 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919638257  

I don't think this is enough. You have to also make sure that the iterators came from the same container. What if you have  
```  
string_view s = "deez nuts";  
decoded_view d1( s );  
decoded_view d2( string_view( s.data(), 4 ) );  
```  
  
now, iterators from d1 and d2 can compare equal when they shouldn't.

> Username: alandefreitas  
> Created_at: 2022-07-13 21:49:34 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920542468  

> when they shouldn't  
  
I'm not sure about that. Views in the STL return equal in this case: https://godbolt.org/z/56vxcdo4n  
  
One might say that people making these decisions that are not smart, etc, and that we should not replicate these mistakes. But it's a recent standard and people are going to expect views of the same container to return equal.

> Username: vinniefalco  
> Created_at: 2022-07-14 03:39:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920722117  

My intuition is that `std::string_view` has different design goals from us, and that we should not follow "make `decoded_view` work like other STL views" too closely. Our design goal is "optimize our container for our use-case." In light of this, functions like `base()` and `length()` are unnecessary. In fact, now that I think about this further, the equalty `pos_ == other.pos_` is OK, but we should add an assert that the iterators are from the same container. This will identify cases where users mistakenly attempt meaningless comparisons.  
  
Perhaps no one will make this mistake, and it won't matter. Or, someone will want to actually perform the comparison using iterators from two different containers, and then open an issue about the assert. If this happens, we will then have an actual use-case that serves as a data point for analysis.

> Username: akrzemi1  
> Created_at: 2022-07-14 05:07:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920754709  

This assumption (compared iterators come from the same container) is no different than the one already present for raw pointers. However, in the case of raw pointers `std::less` and `std::equal` guarantee that pointers from different containers can be compared. The use case is storing the iterators in containers.

> Username: alandefreitas  
> Created_at: 2022-07-14 21:10:24 UTC  
> Updated_at: 2022-07-14 22:54:47 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921575081  

> The use case is storing the iterators in containers  
  
Exactly. Which is effectively also a view. (an expensive view though)

> Username: sehe  
> Created_at: 2022-07-15 13:26:59 UTC  
> Updated_at: 2022-07-15 13:27:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922164816  

I agree with @akrzemi1 . In my mind, the `view` instance has no identity here (outside object identity). Instead it's like an iterator-pair. Different copies of the same iterator pair would (obviously) have iterators that compare equivalent too.

> Username: sehe  
> Created_at: 2022-07-15 13:28:36 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922166255  

(Also being able to compare iterators between subviews seems a useful feature)


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:36:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036751007  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 212 |+ {
 213 |+     BOOST_ASSERT( !empty() );
 214 |+     return *rbegin();
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:36:32 UTC  
> Updated_at: 2022-07-13 04:37:03 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919639093  

The `reverse_iterator` "support" is already of questionable value and try-hard, and now we are making the compiler go through the work of instantiating `*rbegin()`? Why not just return  
```  
*--end()  
```  
?


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:37:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036751681  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 218 |+ decoded_view::size_type
 219 |+ decoded_view::
 220 |+ copy(
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:37:59 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919639628  

this should not be inline


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:38:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036751877  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 241 |+ int
 242 |+ decoded_view::
 243 |+ compare(string_view other) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:38:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919639745  

this should not be inline


---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:39:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036752283  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 270 |+     if( size() == other.size() )
 271 |+         return 0;
 272 |+     return size() < other.size()? -1: +1;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:39:10 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919640030  

You have to write this out manually (without the ternary operator) for coverage or at least put these two cases on separate lines.


---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:40:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036752819  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 314 |+     T& str) const
 315 |+ {
 316 |+     str.append(begin(), end());
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:40:16 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919640440  

At some point a `BOOST_STATIC_ASSERT` would be friendly here, and the javadoc needs to explain what the requirements for `T` are.


---

## Review 44 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:47:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036756658  

📁 include/boost/url/decoded_view.hpp

```diff
  18 |+ #if !defined(BOOST_NO_CXX17_HDR_STRING_VIEW)
  19 |+ #include <string_view>
  20 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:47:49 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919643347  

missing `<iosfwd>`


---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:48:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036756918  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  13 |+ #include <boost/url/decoded_view.hpp>
  14 |+ #include <memory>
  15 |+ #include <new>
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:48:19 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919643528  

missing `<ostream>` and what do we need `<new>` and `<memory>` for?


---

## Review 46 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036757341  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  28 |+ }
  29 |+ 
  30 |+ } // urls
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:10 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919643877  

LOL WUT??? Where's the rest of the file? A goal of our new libraries is _fast compilation time_. That's why we prefer designs that don't require templates, and we take great care to put as many function definitions as possible out-of-line. It looks like we have just slapped "constexpr" on everything possible without really evaluating the costs and benefits. I don't see much of a use-case for comparing lazily percent-decoded ranges at compile time if I'm being honest. So I don't think it is a good decision to mark those things constexpr and then force everyone in the universe to recompile that function every time the header is included.  
  
Contrary to the prevailing Reddit wisdom, we definitely should not "constexpr all the things." Instead we should be thoughtful about where and how we mark things constexpr. There should be an articulable and compelling benefit to its use.

> Username: sehe  
> Created_at: 2022-07-15 13:40:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922176330  

> I don't see much of a use-case for comparing lazily percent-decoded ranges at compile time if I'm being honest  
  
+1. `decoded_strcmp` isn't constexpr anyways, I think?  
  
The idea that inline implementations might lead to some cleverly optimized comparison code in hot code doing comparisons might appeal, but really in practice using specialized string search algorithms (such as c++23's or Boost's) might have more significant impact especially for transforming views. Sadly, it looks as though all the text searchers require random access iterators anyways...


---

## Review 47 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036757813  

📁 test/unit/CMakeLists.txt

```diff
  42 |     const_string.cpp
  43 |+     decoded_view.cpp
  44 |     string_view.cpp
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919644069  

is it my imagination or are these not in alphabetical order? They should be...


---

## Review 48 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036758106  

📁 test/unit/Jamfile

```diff
  45 |     static_url.cpp
  46 |     const_string.cpp
  47 |+     decoded_view.cpp
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:49:51 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919644118  

Same here


---

## Review 49 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:52:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036760112  

📁 include/boost/url/decoded_view.hpp

```diff
 110 |+      */
 111 |+     decoded_view(
 112 |+         char const* str,
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:52:31 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919646258  

but why do we need this overload if we already have one accepting `string_view`?


---

## Review 50 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:53:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036760589  

📁 include/boost/url/decoded_view.hpp

```diff
 156 |+         , dn_(pct_decode_bytes_unchecked(str))
 157 |+         , plus_to_space_(opt.plus_to_space)
 158 |+     {
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:53:28 UTC  
> Updated_at: 2022-07-13 04:53:29 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919646651  

This constructor should not be inline.


---

## Review 51 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 04:56:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036762453  

📁 include/boost/url/decoded_view.hpp

```diff
 154 |+         : p_(str.data())
 155 |+         , n_(str.size())
 156 |+         , dn_(pct_decode_bytes_unchecked(str))
```

> Username: vinniefalco  
> Created_at: 2022-07-13 04:56:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919648133  

This is a case where you don't want to initialize the member in the ctor-init list. For two reasons: 1. you are passing untrusted input to a function that does not perform validation, and 2. you are calculating the decoded size twice (`validate_pct_encoding` returns the size of the resulting decoded string).


---

## Review 52 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:00:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036764603  

📁 include/boost/url/decoded_view.hpp

```diff
 162 |+             return ch != '%';
 163 |+         };
 164 |+         validate_pct_encoding(str, ec, opt, is_unambiguous);
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:00:33 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919649694  

This `is_unambiguous` thing doesn't sound right. You probably want to set `opt.non_normal_is_error = false` and leave out the CharSet parameter. See:  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/pct_encoding.hpp#L100


---

## Review 53 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:01:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036765346  

📁 include/boost/url/decoded_view.hpp

```diff
 198 |+     constexpr
 199 |+     const_iterator
 200 |+     cend() const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:01:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919650286  

Why do we need these? no one is calling cbegin and cend on string views... and no one is going to call these on our type. Lets do an experiment, remove the unnecessary members and lets wait to see if anyone notices.


---

## Review 54 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:12:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036771277  

📁 include/boost/url/decoded_view.hpp

```diff
 436 |+     }
 437 |+ 
 438 |+     /** Returns a basic_string
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:12:39 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919654677  

Needs work

> Username: alandefreitas  
> Created_at: 2022-07-13 22:15:49 UTC  
> Updated_at: 2022-07-13 22:15:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920556386  

I've improved this (locally for now) but I'll let you let me know when we can resolve this.


---

## Review 55 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:14:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036772410  

📁 include/boost/url/decoded_view.hpp

```diff
 445 |+     operator std::basic_string<char, std::char_traits<char>, A>() const
 446 |+     {
 447 |+         return to_string<A>();
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:14:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919655493  

This function should be removed, since `assign_to` does the same job, But I will also point out that this function is broken, it only works when `A` is default constructible, which is almost never the case for custom allocators.


---

## Review 56 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:15:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036772856  

📁 include/boost/url/decoded_view.hpp

```diff
 459 |+     BOOST_URL_CXX20_CONSTEXPR
 460 |+     T&
 461 |+     append_to(T& str) const;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:15:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919655834  

Why do we use the formal parameter name `s` to denote strings everywhere in the library except here where it is now `str`?


---

## Review 57 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:15:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036773078  

📁 include/boost/url/decoded_view.hpp

```diff
 456 |+         entire contents of the decoded range.
 457 |+     */
 458 |+     template <class T>
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919656018  

`T` needs a better label. `String` gets my vote.


---

## Review 58 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:16:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036773402  

📁 include/boost/url/decoded_view.hpp

```diff
 461 |+     append_to(T& str) const;
 462 |+ 
 463 |+     /** Compares two strings
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:16:20 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919656246  

"Return true if two strings are equal"


---

## Review 59 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:16:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036773552  

📁 include/boost/url/decoded_view.hpp

```diff
 467 |+ 
 468 |+         @return true if two strings are equal after percent decoding
 469 |+       */
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:16:35 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919656375  

is your editor adding a stray space to closing C-style comment


---

## Review 60 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:17:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036773895  

📁 include/boost/url/decoded_view.hpp

```diff
 538 |+         @param rhs A string to compare
 539 |+ 
 540 |+         @return true if the corresponding comparison holds after percent decoding
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:17:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919656623  

ugh


---

## Review 61 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:18:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036774506  

📁 include/boost/url/decoded_view.hpp

```diff
 533 |+     }
 534 |+ 
 535 |+     /** Compares two strings
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:18:06 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919657072  

```  
/** Return true if lhs is less than rhs  
  
    The comparison is performed lexicographically after  
    percent-decoding is applied.  
*/  
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:18:29 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919657244  

lhs and rhs don't look great, maybe `s0` and `s1`? What does cppreference use?

> Username: alandefreitas  
> Created_at: 2022-07-14 00:18:07 UTC  
> Updated_at: 2022-07-14 00:18:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920608362  

cppreference uses `lhs` and `rhs`: https://en.cppreference.com/w/cpp/string/basic_string/operator_cmp  
  
should I still change that?

> Username: vinniefalco  
> Created_at: 2022-07-14 02:19:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920692474  

How does it render? Can you paste a screen snippet?

> Username: vinniefalco  
> Created_at: 2022-07-14 02:20:29 UTC  
> Updated_at: 2022-07-14 02:20:30 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920692970  

On second thought, yes I like `s0` and `s1` much better. Rationale: `s` is universally recognized as "string" and zero is less than one which reinforces the left and right sides in a more visual way. lhs and rhs have legibility problems.


---

## Review 62 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:19:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036775293  

📁 include/boost/url/decoded_view.hpp

```diff
 552 |+     friend BOOST_CXX14_CONSTEXPR
 553 |+     bool
 554 |+     operator<(
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:19:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919657638  

What happens if you write  
```  
decoded_view dv;  
if( dv < "sixteen" )  
```  
? I haven't looked at the tests, does this compile?

> Username: alandefreitas  
> Created_at: 2022-07-14 00:24:55 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920610690  

Yes. I included it in the tests.


---

## Review 63 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:20:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036775766  

📁 include/boost/url/decoded_view.hpp

```diff
 579 |+     bool
 580 |+     operator<=(
 581 |+         decoded_view lhs,
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:20:05 UTC  
> Updated_at: 2022-07-13 05:20:06 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919658000  

shouldn't these all be `decoded_view const&`?

> Username: alandefreitas  
> Created_at: 2022-07-14 00:25:50 UTC  
> Updated_at: 2022-07-14 00:25:51 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920611027  

As this is just a range view and only stores the pointers, I'm assuming the same logic for string_view applies here.

> Username: vinniefalco  
> Created_at: 2022-07-14 03:19:47 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920715026  

it is 32 bytes, I think is what matters. but since this is premature optimization, I shouldn't have mentioned it :)


---

## Review 64 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:20:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036776101  

📁 include/boost/url/decoded_view.hpp

```diff
 690 |+         @return true if the corresponding comparison holds after percent decoding
 691 |+       */
 692 |+     template <class StringViewable>
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:20:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919658235  

"StringViewable" doesn't look so great. How about just `String`

> Username: alandefreitas  
> Created_at: 2022-07-14 00:27:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920611426  

I was trying to imply the string type needs to be convertible to a string view. But I guess anyone capable of writing a string type is also able to figure that out.


---

## Review 65 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:21:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036776877  

📁 include/boost/url/decoded_view.hpp

```diff
 817 |+     operator>(
 818 |+             decoded_view lhs,
 819 |+             StringViewable const& rhs) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:21:53 UTC  
> Updated_at: 2022-07-13 05:21:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919658766  

odd indentation but I'm also a little confused. If we have these templates what do we need all the other `string_view` comparison overloads for? Like this one:  
https://github.com/CPPAlliance/url/pull/229/files#diff-7ee7b43158ca027820d486b5a0c5b10621fe2bb4f59c17d14d2d5754202e2423R628


---

## Review 66 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:22:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036777173  

📁 include/boost/url/decoded_view.hpp

```diff
 828 |+         compared.
 829 |+ 
 830 |+         @tparam StringViewable A type from which we can construct a string_view
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:22:22 UTC  
> Updated_at: 2022-07-13 05:22:39 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919658960  

You only use `tparam` when it is not deduced (i.e. when the user specifies it). Instead put the requirements in the description.


---

## Review 67 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:27:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036780193  

📁 include/boost/url/decoded_view.hpp

```diff
 826 |+         @note This function only participates in overload resolution
 827 |+         if we can construct a string_view from the types being
 828 |+         compared.
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:27:20 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919661271  

There's no "we" in docs. You might say instead:  
  
```  
The function only participates in overload resolution when `s1` is convertible to `string_view`.  
```  
  
Come to think of it, `is_convertible` should be used instead of `is_constructible` (you might need to double check that)

> Username: alandefreitas  
> Created_at: 2022-07-14 00:42:13 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920616715  

I believe `is_convertible` will only consider implicit conversions. I have the impression that any type convertible. implicit or explicit, should apply here.

> Username: vinniefalco  
> Created_at: 2022-07-14 14:45:45 UTC  
> Updated_at: 2022-07-14 14:45:46 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921241588  

@pdimov ?

> Username: pdimov  
> Created_at: 2022-07-14 16:05:38 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921326268  

is_convertible only considers implicit conversions, yes. I don't see why types explicitly convertible should work here.


---

## Review 68 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:27:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036780528  

📁 include/boost/url/decoded_view.hpp

```diff
 857 |+         compared.
 858 |+ 
 859 |+         @tparam StringViewable A type from which we can construct a string_view
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:27:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919661535  

no "we"


---

## Review 69 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:31:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036782792  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 133 |+ 
 134 |+ constexpr
 135 |+ decoded_view::const_iterator
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:31:32 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919663260  

uuuugly, consider this instead  
  
```  
auto  
decoded_view::  
begin() const noexcept ->  
    iterator  
...


---

## Review 70 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:33:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036784273  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  57 |+         std::bidirectional_iterator_tag;
  58 |+ 
  59 |+     const_iterator() noexcept = default;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:33:51 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919664372  

isn't it `noexcept` anyway, without the keyword?


---

## Review 71 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:34:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036784880  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  55 |+     using difference_type = std::ptrdiff_t;
  56 |+     using iterator_category =
  57 |+         std::bidirectional_iterator_tag;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:34:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919664880  

how much is bidirectionality costing us and what are we getting out of it?

> Username: alandefreitas  
> Created_at: 2022-07-14 00:47:48 UTC  
> Updated_at: 2022-07-14 00:47:55 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920618597  

I'm not sure I understand.  
  
For most use cases, even in an algorithm that requires random access, I'm pretty sure iterating the view is going to be faster than allocating memory. In all other cases, we can still allocate.  
  
Ergonomics apart, that seems to be the trade-off.

> Username: vinniefalco  
> Created_at: 2022-07-14 03:25:47 UTC  
> Updated_at: 2022-07-14 03:26:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920717064  

we don't need `begin_` if we are ForwardIterator only, so it is costing us at least one pointer. Is iterating backwards useful? What's the use-case? I want us to be in the habit of always evaluating design choices and having an articulable rationale for making them. I would like to discourage doing things "just because."

> Username: alandefreitas  
> Created_at: 2022-07-14 21:31:20 UTC  
> Updated_at: 2022-07-14 22:56:23 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921587674  

Oh... I thought you were talking about the cost in comparison to random access, which is what we had before.  
  
Yes. Then the trade-off is 25% in the size of iterators against the use case of any algorithm that requires a reverse string or going forward and backwards in a string.  
  
The 25% trade-off is easy to measure. But this is such a general operation that it's not easy to list all use cases and their impact. These strings come from URIs and might contain all kinds of data: such as resource paths, things to be used in arbitrary database queries, and so on.   
  
I would imagine almost any use case for iterating strings that way would also be a use case for decoded strings. I'm not an expert but off the top of my head, these would be algorithms involving regular expressions, arithmetical operations on numbers represented as strings, finding rhymes in dictionaries, number base conversion, reverse binary searches to avoid common prefixes, identifying file extensions, and the classic palindrome function in programming interviews :).

> Username: vinniefalco  
> Created_at: 2022-07-14 22:24:34 UTC  
> Updated_at: 2022-07-14 22:24:35 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921623351  

@pdimov ?

> Username: vinniefalco  
> Created_at: 2022-07-14 22:33:02 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921627307  

or @glenfe ?

> Username: sehe  
> Created_at: 2022-07-15 13:22:58 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922161339  

I'm with team bidirectional. Indeed I can see applications wanting to parse directly on it without having to copy into another container temporarily.  
  
The sad irony is that e.g. regex match results will store pairs of iterators, multiplying the added weight per iterator. It would be good to see whether profiling shows significant cost

> Username: vinniefalco  
> Created_at: 2022-07-15 14:05:09 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922199729  

I don't see the use-case for reverse iteration but some fat is always necessary to protect the internal organs I suppose.

> Username: sehe  
> Created_at: 2022-07-15 23:30:36 UTC  
> Updated_at: 2022-07-15 23:30:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922575764  

Mmm. You're right. I had a brainfart. Forward iterators should be fine (just not input only iterators).

> Username: alandefreitas  
> Created_at: 2022-07-16 00:11:38 UTC  
> Updated_at: 2022-07-16 00:11:39 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922585858  

Actually, I just remembered a huge use case for reverse decoded iterators: `operator<(url_view, url_view)` in this very library. The algorithm path comparison in linear time needs to happen in reverse. In fact, the current implementation can be made much smaller now that we have the view.


---

## Review 72 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:35:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036785248  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 112 |+ 
 113 |+     const char*
 114 |+     base()
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:35:28 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919665163  

What's this?

> Username: alandefreitas  
> Created_at: 2022-07-14 00:51:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920619773  

All view range iterators have a `base()` function to get the underlying iterator.  
  
Example: https://en.cppreference.com/w/cpp/ranges/transform_view/iterator

> Username: vinniefalco  
> Created_at: 2022-07-14 03:35:38 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920720787  

But this is no different from calling `encoded().data()`.  What is the use case here? `transform_view` is a class template which is used in generic contexts. We should not just add things because something else has it. Leave it out and wait until a user requests it...

> Username: alandefreitas  
> Created_at: 2022-07-14 21:36:55 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921590670  

The use case is conforming to the standard so that STL algorithms work on the range.   
  
Yes, I know: "People on the committee are not smart", etc... and we should do things differently.   
But it's not in my control that other users will expect us to follow their standards and not expect them to follow our standards.  
  
![](https://imgs.xkcd.com/comics/standards.png)  
  
In any case, keeping this is not something I'm very attached to. I'm not emotionally attached to anything. This is just the rationale.


---

## Review 73 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:43:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036790380  

📁 test/unit/decoded_view.cpp

```diff
 287 |+             BOOST_TEST_NOT(s == str1);
 288 |+             BOOST_TEST(s == bstr0);
 289 |+             BOOST_TEST_NOT(s == bstr1);
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:43:50 UTC  
> Updated_at: 2022-07-13 05:43:51 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919668821  

Consider this:  
```  
            BOOST_TEST(s == s0);  
            BOOST_TEST(s == str0);  
            BOOST_TEST(s == bstr0);  
            BOOST_TEST_NOT(s == s1);  
            BOOST_TEST_NOT(s == str1);  
            BOOST_TEST_NOT(s == bstr1);  
```


---

## Review 74 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:44:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036790952  

📁 test/unit/decoded_view.cpp

```diff
 334 |+             decoded_view s(str);
 335 |+             std::string o = "to be recycled";
 336 |+             BOOST_TEST_EQ(s.to_string(o), dec_str);
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:44:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919669199  

This should be `s.assign_to(o)`.


---

## Review 75 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:45:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036791648  

📁 test/unit/decoded_view.cpp

```diff
 343 |+         }
 344 |+ 
 345 |+         // explicit operator basic_string()
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:45:43 UTC  
> Updated_at: 2022-07-13 05:45:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919669698  

I don't think we should provide this function. The user should have to call `to_string()` explicitly. Otherwise it hides a potentially expensive operation. We should go to review without the function, and let the reviewers argue the case for including it if they think it is necessary.


---

## Review 76 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:47:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036792492  

📁 test/unit/decoded_view.cpp

```diff
 380 |+             s.append_to(o);
 381 |+             std::string exp = std::string("init ");
 382 |+             exp += dec_str;
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:47:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919670387  

so we are converting `dec_str` to a basic_string implicitly? oof... if there's a big SQL expression that's a lot of temporary allocations.

> Username: alandefreitas  
> Created_at: 2022-07-14 00:57:02 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920621642  

I'm not sure I understand the question, but `exp` is the expected string result, and I'm just using `dec_str` to assemble the expected results. This is not touching and has nothing to do with `decoded_view` at all.

> Username: vinniefalco  
> Created_at: 2022-07-14 14:46:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921242623  

is `dec_str` a decoded view?

> Username: alandefreitas  
> Created_at: 2022-07-14 21:38:47 UTC  
> Updated_at: 2022-07-14 21:38:48 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921591668  

No.   
  
```cpp  
string_view dec_str = "a uri test";  
```  
  
It's just a fixture with the expected result of that test.   
  
It has nothing to do with what users will do with a decoded_view.


---

## Review 77 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-13 05:47:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1036792916  

📁 test/unit/decoded_view.cpp

```diff
 409 |+         {
 410 |+             BOOST_STATIC_ASSERT(
 411 |+                 sizeof(urls::decoded_view) ==
```

> Username: vinniefalco  
> Created_at: 2022-07-13 05:47:41 UTC  
> Updated_at: 2022-07-13 05:47:42 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r919670722  

eh.. this static assert is questionable

> Username: alandefreitas  
> Created_at: 2022-07-14 00:57:42 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920621861  

Oh... this is something I replicated from PR127 to prove a point. It doesn't make any sense anymore.


---

## Comment 78

> Username: cppalliance-bot  
> Created_at: 2022-07-14 01:04:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1183836828  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 79 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:00:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038163481  

📁 include/boost/url/decoded_view.hpp

```diff
 167 |+     /** Returns the number of char elements in the string
 168 |+     */
 169 |+     size_type
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:00:08 UTC  
> Updated_at: 2022-07-14 02:00:09 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920652684  

if you want to declare this and the others like it `constexpr`, you could, and it would cost us nothing (as long as we don't have to use those ugly CONSTEXPR_CXX macros)


---

## Review 80 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:01:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038164192  

📁 include/boost/url/decoded_view.hpp

```diff
 181 |+     }
 182 |+ 
 183 |+     /** Copies a decoded substring to another character string
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:01:29 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920653211  

Briefs should use the imperative voice not the passive voice. For example:  
```  
/** Copy decoded characters to a destination buffer  
```


---

## Review 81 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:02:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038164511  

📁 include/boost/url/decoded_view.hpp

```diff
 191 |+         Exceptions thrown on invalid positions.
 192 |+ 
 193 |+         @throw std::out_of_range If `pos > size()`
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:02:03 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920653428  

How does this render?

> Username: alandefreitas  
> Created_at: 2022-07-14 21:42:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921593851  

<div class="informaltable"><table class="table">  
<colgroup>  
<col>  
<col>  
</colgroup>  
<thead><tr>  
<th>  
                  <p>  
                    Type  
                  </p>  
                </th>  
<th>  
                  <p>  
                    Thrown On  
                  </p>  
                </th>  
</tr></thead>  
<tbody><tr>  
<td>  
                  <p>  
                    <code class="computeroutput"><span class="identifier">std</span><span class="special">::</span><span class="identifier">out_of_range</span></code>  
                  </p>  
                </td>  
<td>  
                  <p>  
                    If <code class="computeroutput"><span class="identifier">pos</span> <span class="special">&gt;</span>  
                    <span class="identifier">size</span><span class="special">()</span></code>  
                  </p>  
                </td>  
</tr></tbody>  
</table></div>

> Username: vinniefalco  
> Created_at: 2022-07-14 22:28:44 UTC  
> Updated_at: 2022-07-14 22:28:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921625238  

That's not a screen capture you just made it up. Does "If" belong there?

> Username: alandefreitas  
> Created_at: 2022-07-14 23:02:01 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921639339  

> That's not a screen capture   
  
I agree  
  
> you just made it up.   
  
I disagree.   
  
I copied and pasted the HTML from the reference exactly. Because these screenshots are very noisy and, in most cases, do not convey more information than the HTML contents.  
  
You may not like it. But it's not made up.  
  
In any case, there is the screenshot:  
  
![image](https://user-images.githubusercontent.com/5369819/179113656-4288bd20-c4c6-4cc7-b8a0-fe636615f509.png)  
  
  
> Does "If" belong there?  
  
Yes. I just removed it. All the rest looks like other instances in the library.


---

## Review 82 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:16:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038213616  

📁 include/boost/url/decoded_view.hpp

```diff
 268 |+         overload resolution if the container
 269 |+         type `T` has the member function
 270 |+         `assign(iterator, iterator)`.
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:16:47 UTC  
> Updated_at: 2022-07-14 02:16:48 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920691566  

This is not correct. The function should always be found (no SFINAE) and we should put a BOOST_STATIC_ASSERT in the definition instead. This helps the user with better diagnostics, and you can put a comment near the assert explaining the requiremetns.


---

## Review 83 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:17:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038214004  

📁 include/boost/url/decoded_view.hpp

```diff
 285 |+         overload resolution if the container
 286 |+         type `T` has the member function
 287 |+         `append(iterator, iterator)`.
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:17:38 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920691902  

Same here. Don't use SFINAE use BOOST_STATIC_ASSERT. Your javadoc says `T` when you mean `String`. You should require `String` have a nested `value_type` that is `char`.

> Username: vinniefalco  
> Created_at: 2022-07-14 03:18:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920714746  

lets rename this to `MutableString` and add a trait `is_mutable_string` which we can use in the static assert

> Username: alandefreitas  
> Created_at: 2022-07-14 21:46:19 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921595800  

We already have `detail::has_assign_from` and `detail::has_append_from` to make the static assert easier to read. Should `is_mutable_string` include both conditions? Should we make it public? Where should it go?


---

## Review 84 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 02:18:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038214311  

📁 include/boost/url/decoded_view.hpp

```diff
 370 |+ #ifndef BOOST_URL_DOCS
 371 |+     typename std::enable_if<
 372 |+         std::is_constructible<string_view, String>::value, bool
```

> Username: vinniefalco  
> Created_at: 2022-07-14 02:18:14 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920692120  

shouldn't this be `is_convertible`?


---

## Review 85 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:12:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038240817  

📁 include/boost/url/decoded_view.hpp

```diff
 114 |+         , n_(0)
 115 |+         , dn_(0)
 116 |+         , plus_to_space_(opt.plus_to_space)
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:12:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920712519  

if you already have in-class initializers what's the purpose of repeating it in the ctor-init list?


---

## Review 86 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:13:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038241093  

📁 include/boost/url/decoded_view.hpp

```diff
 134 |+     end() const noexcept;
 135 |+ 
 136 |+     /** Accesses the first decoded character
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:13:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920712762  

Start the brief with the word "Return"


---

## Review 87 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:13:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038241329  

📁 include/boost/url/decoded_view.hpp

```diff
 136 |+     /** Accesses the first decoded character
 137 |+ 
 138 |+         @note The behavior is undefined if `empty() == true`.
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:13:54 UTC  
> Updated_at: 2022-07-14 03:14:30 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920712932  

```  
@par Preconditions  
  
empty() != true  
```  
  
Didn't you create this cheat sheet?  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/doc/javadoc.hpp#L20


---

## Review 88 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:15:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038241878  

📁 include/boost/url/decoded_view.hpp

```diff
 173 |+     }
 174 |+ 
 175 |+     /** Returns whether the string is empty
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:15:07 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920713361  

"Return true if the string is empty"


---

## Review 89 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:17:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038242827  

📁 include/boost/url/decoded_view.hpp

```diff
 205 |+          size_type pos = 0) const;
 206 |+ 
 207 |+     /** Return true if the corresponding comparison holds
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:17:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920714141  

The brief is completely wrong... how about  
"Return the result of comparing to another string"  
  
And the most important aspect of this function is not mentioned, that the comparison is performed after percent-decoding is applied.


---

## Review 90 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:17:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038243141  

📁 include/boost/url/decoded_view.hpp

```diff
 226 |+     /// @copydoc compare()
 227 |+     int
 228 |+     compare(char const* other) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:17:52 UTC  
> Updated_at: 2022-07-14 03:17:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920714367  

But why do we need this overload if we have `string_view`? Does it not compile otherwise?


---

## Review 91 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:22:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038245241  

📁 include/boost/url/decoded_view.hpp

```diff
 364 |+         @note This function only participates in
 365 |+         overload resolution if when a `string_view`
 366 |+         is constructible from a `String`.
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:22:32 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920715946  

Don't we use `@par Constraints`?  
  
```  
        @par Constraints  
        @code  
        std::is_convertible_v< String, string_view >  
        @endcode  
```  
  
Check out this javadoc, it has a little bit of everything:  
https://github.com/boostorg/json/blob/43d04d1d214e29d1ad890c5f9237ae0201726166/include/boost/json/array.hpp#L1241


---

## Review 92 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:23:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038245733  

📁 include/boost/url/decoded_view.hpp

```diff
 638 |+     std::ostream&
 639 |+     operator<<(std::ostream& os,
 640 |+         decoded_view const& cs);
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:23:39 UTC  
> Updated_at: 2022-07-14 03:23:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920716282  

mismatch in @param and formal argument name


---

## Review 93 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:23:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038245832  

📁 include/boost/url/detail/config.hpp

```diff
  78 |+     # define BOOST_URL_CXX20_CONSTEXPR
  79 |+ #else
  80 |+     # define BOOST_URL_CXX20_CONSTEXPR constexpr
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:23:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920716353  

Are we still using these?


---

## Review 94 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:24:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038246050  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  16 |+ class decoded_view::iterator
  17 |+ {
  18 |+     char const* begin_{nullptr};
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:24:24 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920716529  

why do you use curly braces instead of equals? I think this is ok here but in general it can lead to trouble, especially with user-defined types.

> Username: alandefreitas  
> Created_at: 2022-07-14 23:03:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921640136  

I don't know. I think I'm used to seeing curly braces more often but I don't care as much. Just changed it.


---

## Review 95 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:32:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038250421  

📁 include/boost/url/impl/decoded_view.hpp

```diff
  90 |+         if (pos_ - begin_ < 3 ||
  91 |+             pos_[-3] != '%')
  92 |+             --pos_;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:32:35 UTC  
> Updated_at: 2022-07-14 03:32:57 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920719526  

I indent the last line of a multiline condition when the body of the `if` has a single statement with no braces, to make the body stand out:  
```  
        if (pos_ - begin_ < 3 ||  
                pos_[-3] != '%')  
            --pos_;  
```  
  
But that's just style

> Username: alandefreitas  
> Created_at: 2022-07-14 23:05:20 UTC  
> Updated_at: 2022-07-14 23:05:31 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921640689  

I fixed it, but I'm not sure this is going to be easy to remember next time. You'll probably have to warn me again.


---

## Review 96 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:40:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038253761  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 137 |+ decoded_view::
 138 |+ begin() const noexcept ->
 139 |+     decoded_view::const_iterator
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:40:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920722392  

yeah this looks nicer, although.. do you still need the namespace qualifier?


---

## Review 97 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:40:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038253932  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 152 |+ 
 153 |+ inline
 154 |+ decoded_view::const_reference
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:40:48 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920722512  

could use `auto` here to skip the namespace qualifier


---

## Review 98 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:41:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038254016  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 154 |+ decoded_view::const_reference
 155 |+ decoded_view::
 156 |+ front() const
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:41:00 UTC  
> Updated_at: 2022-07-14 03:41:15 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920722585  

should this be `noexcept`? I guess not since it has a precondition, although Peter might disagree.


---

## Review 99 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:42:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038254728  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 206 |+     String& s) const
 207 |+ {
 208 |+     BOOST_STATIC_ASSERT(
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:42:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920723126  

You need a comment here:  
```  
// If this goes off, it means that your String type  
// is missing the necessary append or assign member  
// functions, or that your value_type is not char!  
```


---

## Review 100 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:43:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038255182  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 189 |+ 
 190 |+ template<class C, class I>
 191 |+ struct has_append_from<C, I, boost::void_t<
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:43:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920723469  

I think we might roll these both up into a single trait that checks for both, and also checks that `value_type` is `char`. And we might call it `is_mutable_string`. I would use this in Boost.HTTP.Proto, so we might consider making it public and putting it in `urls::grammar`

> Username: alandefreitas  
> Created_at: 2022-07-14 22:00:18 UTC  
> Updated_at: 2022-07-14 22:00:19 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921603240  

`boost/url/grammar/is_mutable_string.hpp`?

> Username: vinniefalco  
> Created_at: 2022-07-14 22:26:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921624178  

Hmm I am not sure, maybe start there and if we build up more algorithms then we might rename it. For example we could do free-function versions of `append_to` and `assign_to` which accept an InputIterator range of char and outputs to a MutableString

> Username: alandefreitas  
> Created_at: 2022-07-14 23:08:16 UTC  
> Updated_at: 2022-07-14 23:08:17 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921641808  

> maybe start there  
  
Do you mean "start there" as in "leave it as it is until we have a use case for a public trait" or "maybe start there" as in "create the public trait so maybe we can come up with algorithms that use it"?

> Username: vinniefalco  
> Created_at: 2022-07-15 01:59:53 UTC  
> Updated_at: 2022-07-15 02:00:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921742473  

Start with it in grammar/is_mutable_string.hpp. We can do this after merging the PR if you want.


---

## Review 101 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:45:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038255858  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  27 |+     error_code ec;
  28 |+     opt.non_normal_is_error = false;
  29 |+     dn_ = validate_pct_encoding(
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:45:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920723999  

better, and yeah we need to fix this CharSet debacle, lol


---

## Review 102 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:45:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038255936  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  35 |+ }
  36 |+ 
  37 |+ decoded_view::size_type
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:45:15 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920724062  

`auto` with trailing return?


---

## Review 103 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:46:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038256715  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  48 |+     auto first = begin();
  49 |+     while (pos--)
  50 |+         ++first;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:46:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920724646  

`first = std::next( first, pos );`

> Username: alandefreitas  
> Created_at: 2022-07-14 22:04:22 UTC  
> Updated_at: 2022-07-14 22:04:23 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921613522  

Yes.  
  
It would probably be   
  
```cpp  
auto first = std::next( begin(), pos );  
```  
  
or   
  
```cpp  
auto first = begin();  
std::advance( first, pos );  
```  
  
I thought you didn't want me to use these functions to avoid the includes. But the include is there anyway because of the iterator categories.


---

## Review 104 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:47:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038257144  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  51 |+     auto last = first;
  52 |+     while (rlen--)
  53 |+         ++last;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:47:29 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920724893  

`last = std::next( first, rlen );`


---

## Review 105 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:48:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038257551  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  53 |+         ++last;
  54 |+     for (; first != last; ++first, ++dest)
  55 |+         *dest = *first;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:48:19 UTC  
> Updated_at: 2022-07-14 03:48:20 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920725192  

you could have written  
```  
while( first != last )  
    *dest++ = *first++;  
```


---

## Review 106 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:50:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038258497  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  83 |+             break;
  84 |+         }
  85 |+     }
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:50:25 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920725882  

This loop has some pretty significant inefficiencies, can you find them?

> Username: alandefreitas  
> Created_at: 2022-07-14 22:11:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921617411  

I improved it a little, but you probably want to check it again.

> Username: vinniefalco  
> Created_at: 2022-07-14 22:37:16 UTC  
> Updated_at: 2022-07-14 22:37:47 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921629031  

Each iterator is being checked THREE times for a percent. And the `while` can be simplified to use a counter, because we know in advance if one decoded string is shorter. Furthermore, when we calculate that one of the strings is shorter, we can use that information to avoid the additional conditions on lines 97 and 99. Consult a generic implementation of 3-way compare for tips.  
  
This is optimization so I don't really care about it right now, if you want to just open an issue and play with it some other time that is perfectly fine too.

> Username: alandefreitas  
> Created_at: 2022-07-14 23:31:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921691615  

I had a look at `strcmp` and tried to improve it a little more.

> Username: vinniefalco  
> Created_at: 2022-07-15 02:05:35 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921744383  

Yeah its better now! Still can do better though :)


---

## Review 107 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038260375  

📁 include/boost/url/impl/decoded_view.ipp

```diff
 122 |+     if( size() == other.size() )
 123 |+         return 0;
 124 |+     return size() < other.size()? -1: +1;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:54:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920727144  

I think I would just prefer if we didn't use the ternary anymore...


---

## Review 108 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 03:54:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1038260650  

📁 test/unit/CMakeLists.txt

```diff
  17 |-     CMakeLists.txt
  18 |-     Jamfile
  19 |-     test_rule.hpp
```

> Username: vinniefalco  
> Created_at: 2022-07-14 03:54:37 UTC  
> Updated_at: 2022-07-14 03:55:32 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r920727365  

We need test_rule.hpp here or else it will not be in the Visual Studio Project explorer:  
  
![image](https://user-images.githubusercontent.com/1503976/178894935-c4913ddd-e2f3-419f-90f2-650884d989fc.png)


---

## Comment 109

> Username: akrzemi1  
> Created_at: 2022-07-14 04:32:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1183975381  

If `decoded_view` is employed in the library, my understanding is that the following (among others) things will change:  
  
1. Alias `urls::segments_view::reference_type` will become `decoded_view`.  
2. Funciton `urls::url_view::segments()` will no longer take an allocator as its argument.  
  
Am I right?

---

## Comment 110

> Username: akrzemi1  
> Created_at: 2022-07-14 04:46:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1183982547  

So, `encoded_view` gives me the following options:  
  
1. I can inspect its characters (in particular use comparison operators) without no-allocation guarantee.  
2. I can use it to populate a different `char` container, deferring the allocation strategy to the user-provided argument (functions `assign_to` and `append_to`).  
3. I can just convert to `std::string` and allocate.  
  
Is that right?  
  
So, `encoded_view` will not allocate but instead build decoded values on the fly. The cost of this is that the iterator is no longer contiguous and the compiler cannot use efficient algorithms such as `memcpy` and `memcmp` for efficient string operations. This trade-off is not obvious for me, and I guess it can pessimize some use cases.   
  
Could we see how the example with building a filesystem path from the `url_view` will change after this PR is applied?

---

## Comment 111

> Username: vinniefalco  
> Created_at: 2022-07-14 14:04:24 UTC  
> Updated_at: 2022-07-14 14:12:58 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1184489636  

> Could we see how the example with building a filesystem path from the url_view will change after this PR is applied?  
  
The example doesn't change, because `fs::path` is constructible from `decoded_view`. And furthermore, it does so without allocating an extra temporary variable. The same comparisons on path segments also work without an additional allocation for a contiguous decoded buffer. It is quite nice :) This is what it looks like:  
```  
        // Append following segments to root  
        result = root_;  
        while (it0 != end0)  
        {  
            result /= *it0;  
            ++it0;  
        }  
```  
  
`result` is an out-param instead of a return value, so that the `fs::path` can be reused, avoiding subsequent allocations. This is the purpose of the `append_to` and `assign_to` APIs.

---

## Comment 112

> Username: akrzemi1  
> Created_at: 2022-07-14 15:33:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1184590465  

Currently function url_view::segments() takes an allocator as argument. Is it going to change?

---

## Comment 113

> Username: vinniefalco  
> Created_at: 2022-07-14 16:56:34 UTC  
> Updated_at: 2022-07-14 16:57:10 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1184674205  

> Currently function url_view::segments() takes an allocator as argument. Is it going to change?  
  
Yes, `const_string`, the factory, and the allocator arguments will all go away. We might put an optional allocator into `decoded_view::to_string()`. But even without it, you can always `assign_to` or `append_to` a `std::basic_string` with a custom allocator. In fact assign and append will work with any "mutable string-like" including `json::string`, `boost::string`, and so on. `std::filesystem::path` has something similar to this (they call it a "Source"). We feel that this concept is something useful that can be used in a variety of contexts (in particular to give the user flexible tools to avoid dynamic allocation).

---

## Review 114 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 17:00:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039192294  

📁 test/unit/decoded_view.cpp

```diff
  21 |+ struct decoded_view_test
  22 |+ {
  23 |+     using A = std::allocator<char>;
```

> Username: vinniefalco  
> Created_at: 2022-07-14 17:00:03 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921374417  

Are we using `A` anymore?


---

## Comment 115

> Username: cppalliance-bot  
> Created_at: 2022-07-14 22:29:55 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1184955814  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 116 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-14 22:34:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039557228  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 200 |+     // This function should only be used
 201 |+     // if the container `MutableString` has the
 202 |+     // member function `assign(iterator, iterator)`.
```

> Username: vinniefalco  
> Created_at: 2022-07-14 22:34:51 UTC  
> Updated_at: 2022-07-14 22:34:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921628112  

NICE !!!


---

## Comment 117

> Username: alandefreitas  
> Created_at: 2022-07-14 23:52:31 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185033444  

> my understanding is that the following (among others) things will change:  
  
100% correct.  
  
> I can inspect its characters (in particular use comparison operators) without no-allocation guarantee.  
  
Actually, you have a no-allocation guarantee. At least no extra allocations for getting the decoded view of the string.  
  
> I can use it to populate a different char container, deferring the allocation strategy to the user-provided argument  
  
Correct. And also any container that accepts the iterators to or ranges of `char` really. At worst, `const_string(my_decoded_view)` would be no worse than what we had before.  
  
> The cost of this is that the iterator is no longer contiguous  
  
In that case, I would maybe say the cost is worse ergonomics when you need a contiguous iterator because you have an extra step.  
  
> the compiler cannot use efficient algorithms such as memcpy and memcmp for efficient string operations.   
> This trade-off is not obvious for me, and I guess it can pessimize some use cases.  
  
Yes. And the contiguous iterator would come at the cost of making one copy without `memcpy` before being able to `memcpy`. Because `memcpy` cannot decode as it copies.  
  
So there's no use case where it's worth making a copy without `memcpy` to be able to make a copy with `memcpy` because a copy without `memcpy` is expensive. In terms of trade-offs, because of the previous copy, the lazy range will always be pareto optimal. Even though the first copy would happen as an implementation detail.  
  
Also, if and only if this is the use case, you can still use these algorithms before that extra-step. And the final cost is not higher even in that case. In practice, we haven't seen that use case in the examples we came up with.  
  
> Could we see how the example with building a filesystem path from the url_view will change after this PR is applied?  
  
We initially had the examples in this PR as an experiment, but we removed them to avoid conflict and be able to replace `const_string` later in another PR.   
  
When we had the examples, we had to change the segment/params iterators, but the examples didn't really change that much. The only real difference was we didn't need to create the pools and the allocators. We just specialized `fs::path_traits` and appending to a `fs::path` worked as usual. The only difference is it avoided two or three allocations depending on the case.  
  
> Currently function url_view::segments() takes an allocator as argument. Is it going to change?  
  
Yes. The user is in total control. Even to never allocate and work with the reference on the fly. Or to use different allocators for different segments or whatever comes to mind.

---

## Comment 118

> Username: cppalliance-bot  
> Created_at: 2022-07-15 01:04:58 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185067700  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 119

> Username: cppalliance-bot  
> Created_at: 2022-07-15 01:34:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185081669  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 120 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 01:55:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039699731  

📁 include/boost/url/decoded_view.hpp

```diff
 185 |+         Exceptions thrown on invalid positions.
 186 |+ 
 187 |+         @throw std::out_of_range `pos > size()`
```

> Username: vinniefalco  
> Created_at: 2022-07-15 01:55:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921740875  

This is a Precondition, no?


---

## Review 121 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 01:56:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039700218  

📁 include/boost/url/decoded_view.hpp

```diff
 259 |+         entire contents of the decoded range.
 260 |+     */
 261 |+     template <class MutableString>
```

> Username: vinniefalco  
> Created_at: 2022-07-15 01:56:26 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921741288  

You need to add a Concept to the docs, see Beast

> Username: alandefreitas  
> Created_at: 2022-07-15 02:49:43 UTC  
> Updated_at: 2022-07-15 02:49:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921759396  

Done. However, I just noticed `vector<char>` won't have `append`. Maybe we can replace the constraint `s.append(first, last)` with another constraint `s.insert(s.end(), first, last)`.

> Username: vinniefalco  
> Created_at: 2022-07-15 03:36:13 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921783100  

lets leave `vector<char>` out of it for now, and see what people say?


---

## Review 122 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 01:57:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039700581  

📁 include/boost/url/decoded_view.hpp

```diff
 355 |+     typename std::enable_if<
 356 |+         std::is_convertible<String, string_view>::value &&
 357 |+         !std::is_same<typename std::decay<String>::type, decoded_view>::value,
```

> Username: vinniefalco  
> Created_at: 2022-07-15 01:57:15 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921741591  

You added an additional constraint without updating the javadoc. Was this needed to fix a compile error?

> Username: alandefreitas  
> Created_at: 2022-07-15 02:51:18 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921759975  

That was a compiler error on a few old compilers that can't identify the overload with `decoded_view` should be preferred over the overload with the template.

> Username: vinniefalco  
> Created_at: 2022-07-15 03:35:32 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921782832  

Good, then we shouldn't mention it in the javadoc


---

## Review 123 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 01:57:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039700886  

📁 include/boost/url/decoded_view.hpp

```diff
 368 |+     }
 369 |+ 
 370 |+     /** Return true if two strings are unequal
```

> Username: vinniefalco  
> Created_at: 2022-07-15 01:57:56 UTC  
> Updated_at: 2022-07-15 01:58:06 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921741847  

"not equal" might be more idiomatic


---

## Review 124 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 01:58:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039701200  

📁 include/boost/url/decoded_view.hpp

```diff
 681 |+         std::ostream& os,
 682 |+         decoded_view const& s);
 683 |+ };
```

> Username: vinniefalco  
> Created_at: 2022-07-15 01:58:36 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921742038  

The class is much leaner now, thanks


---

## Review 125 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 02:01:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039702622  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 195 |+     MutableString& s) const
 196 |+ {
 197 |+     // If this goes off, it means that your String type
```

> Username: vinniefalco  
> Created_at: 2022-07-15 02:01:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921743150  

"If this goes off" is what you write for an assert. For a static assert, start the sentence with "If you get a compile error here"


---

## Review 126 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 02:02:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039702773  

📁 include/boost/url/impl/decoded_view.hpp

```diff
 198 |+     // is missing the necessary append or assign member
 199 |+     // functions, or that your value_type is not char!
 200 |+     // This function should only be used
```

> Username: vinniefalco  
> Created_at: 2022-07-15 02:02:01 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921743250  

Maybe insert a blank comment before "This function"


---

## Review 127 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 02:03:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039703425  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  71 |+     auto last = std::next(first, rlen);
  72 |+     while (first != last)
  73 |+         *dest++ = *first++;
```

> Username: vinniefalco  
> Created_at: 2022-07-15 02:03:33 UTC  
> Updated_at: 2022-07-15 02:03:34 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921743743  

hmm this got cleaner! :)


---

## Review 128 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 02:05:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039704427  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  79 |+ template <class T>
  80 |+ int
  81 |+ decoded_strcmp(decoded_view s0, T s1)
```

> Username: vinniefalco  
> Created_at: 2022-07-15 02:05:58 UTC  
> Updated_at: 2022-07-15 02:05:59 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921744514  

Oh nice this works for both T


---

## Comment 129

> Username: vinniefalco  
> Created_at: 2022-07-15 02:07:26 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185094251  

I left comments, not much more change although we should decide if we want `pct_decoded_str`? or `pct_decoded_view`? After renaming it is up to you when to merge it - whether you want to wait for more feedback or not.

---

## Comment 130

> Username: cppalliance-bot  
> Created_at: 2022-07-15 03:09:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185129511  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 131

> Username: cppalliance-bot  
> Created_at: 2022-07-15 04:04:56 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185151262  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 132 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 04:43:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039786106  

📁 doc/Jamfile

```diff
  41 |+             \"BOOST_CXX14_CONSTEXPR=constexpr\" \\
  42 |+             \"BOOST_URL_CXX17_CONSTEXPR=constexpr\" \\
  43 |+             \"BOOST_URL_CXX20_CONSTEXPR=constexpr\" \\
```

> Username: vinniefalco  
> Created_at: 2022-07-15 04:43:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921805980  

are we still using these?


---

## Review 133 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 04:44:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039786304  

📁 doc/qbk/6.2.MutableString.qbk

```diff
   1 |+ [/
   2 |+     Copyright (c) 2021 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2022-07-15 04:44:20 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921806105  

Your name please


---

## Review 134 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 04:44:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1039786392  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  13 |+ `append` member functions that accept iterators to `char`.
  14 |+ 
  15 |+ The library __decoded_view__ class provides a operations
```

> Username: vinniefalco  
> Created_at: 2022-07-15 04:44:31 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921806170  

grammar

> Username: alandefreitas  
> Created_at: 2022-07-15 04:51:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r921808725  

What?

> Username: sehe  
> Created_at: 2022-07-15 13:15:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922154507  

`The library class __decoded_view__` (or maybe drop library altogether)?

> Username: vinniefalco  
> Created_at: 2022-07-15 14:05:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922200334  

The whole sentence is a train wreck lol

> Username: alandefreitas  
> Created_at: 2022-07-15 16:26:41 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922324541  

Oh... You thought you were talking about the `grammar` namespace in your first comment! :laughing:


---

## Comment 135

> Username: alandefreitas  
> Created_at: 2022-07-15 04:50:41 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185171307  

> I left comments, not much more change although we should decide if we want pct_decoded_str? or pct_decoded_view? After renaming it is up to you when to merge it - whether you want to wait for more feedback or not.  
  
I included the rationale for the name in the description but your suggestions brought up some ideas I didn't think about.  
  
- About the `pct_` prefix:   
    - `pct_decoded` might not be so much less ergonomic than `decoded` because this is often going to be used as `auto`.  
    - the rationale for `decoded` was that the prefix `pct_decoded` would be longer, less ergonomic, and doesn't add much information over `decoded` because percent-encoding is the only implication possible in the context.   
    - Besides that rationale, to be honest, as a "user", it took me a while to understand that the `pct` prefix meant "percent". I just realized that when I did read the code.   
- About the `view` suffix:  
    - At first, the only options we had considered were `view` and `range`. Considering the convention used in the standard, `range` could imply it owns the data while `view` ensures it does not.  
    - Now that the new `_str` is on the table, I'm not sure. The `_str` might sound like it owns the data. This might lead to problems and issues to solve if people make that assumption.  
  
Another alternative would be to see what other people think about it. This might lead to a more "natural" name, but the problem is haven't got a lot of feedback so far.

---

## Comment 136

> Username: cppalliance-bot  
> Created_at: 2022-07-15 05:05:12 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185177450  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 137

> Username: codecov[bot]  
> Created_at: 2022-07-15 08:02:39 UTC  
> Updated_at: 2022-07-15 18:12:33 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185286155  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#229](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (0c25e44) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/ef2db3c1e52474b881812da92ae495cd936ed150?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (ef2db3c) will **decrease** coverage by `0.00%`.  
> The diff coverage is `96.62%`.  
  
> :exclamation: Current head 0c25e44 differs from pull request most recent head 10ca89d. Consider uploading reports for the commit 10ca89d to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/229/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #229      +/-   ##  
===========================================  
- Coverage    96.78%   96.78%   -0.01%       
===========================================  
  Files          120      123       +3       
  Lines         6068     6216     +148       
===========================================  
+ Hits          5873     6016     +143       
- Misses         195      200       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/decoded\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/229/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGVkX3ZpZXcuaXBw) | `93.75% <93.75%> (ø)` | |  
| [include/boost/url/impl/decoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/229/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGVkX3ZpZXcuaHBw) | `97.87% <97.87%> (ø)` | |  
| [include/boost/url/decoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/229/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlZF92aWV3LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [ef2db3c...10ca89d](https://codecov.io/gh/CPPAlliance/url/pull/229?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 138 [Commented]

> Username: sehe  
> Created_at: 2022-07-15 13:31:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040300347  

📁 include/boost/url/impl/decoded_view.ipp

```diff
  28 |+         return *pos_;
  29 |+     char d0;
  30 |+     grammar::hexdig_value(pos_[1], d0);
```

> Username: sehe  
> Created_at: 2022-07-15 13:31:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922168386  

is there a reason why d0 cannot be unsigned from the start? Seems more sensible to me. And - less typing!

> Username: vinniefalco  
> Created_at: 2022-07-15 13:48:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922184587  

`hexdig_value` returns -1 on error

> Username: alandefreitas  
> Created_at: 2022-07-15 16:30:07 UTC  
> Updated_at: 2022-07-15 16:30:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922327095  

actually `hexdig_value` returns a `bool`.


---

## Comment 139

> Username: sehe  
> Created_at: 2022-07-15 13:41:04 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185558402  

What I like most about this PR is that it documents the design process very well. The linked analysis are a treasure trove, and also pin down the focus.  
  
> A url_view has functions such as url_view::XXXXXX() and url_view::encoded_XXXXXX() for all URI components, where the default function url_view::XXXXXX() is the expensive one. Some people have found this counterintuitive  
  
I somewhat agree. Making the invariant ("url always stores a valid url") more conspicuos in the intro documentation could help. The _obvious_ solution might be to switch to `url_view::XXXXX()` and `url_view::decoded_XXXXX()`...  
  
I went through the current changes and I like the simplicity. I wasn't a fan of  lazy views, but I'm warming up seeing the simplicity.

---

## Comment 140

> Username: vinniefalco  
> Created_at: 2022-07-15 14:03:09 UTC  
> Updated_at: 2022-07-15 14:03:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185578429  

> I included the rationale for the name in the description but your suggestions brought up some ideas I didn't think about.  
  
I think "decoded" is misleading and incorrect. When you parse a string with percent-encoding, you expect to get a percent-encoded string not a decoded string. If a function accepts a string with percent encoding, you don't expect to see the type of parameter to be "decoded." In fact there is no such thing as a "percent-decoded string" - this is just a plain string, with no encoding. That is why you never see me say "returns a percent decoded string" in javadocs. Instead I use the phrase "string with percent-decoding applied" as you can see here:  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/url_view.hpp#L726  
  
The current library type `pct_encoded_str` was written to fit the use case identical to that which `decoded_view` intends to fit, but it was never developed beyond the current declaration:  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/pct_encoding_types.hpp#L22  
  
Users who wish to parse a "percent-encoded string" robustly will use `grammar::parse` with the `urls::pct_encoded_rule`:  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/rfc/pct_encoded_rule.hpp#L38  
  
After parsing they will of course expect to receive a "percent-encoded string", and that is what they get:  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/rfc/pct_encoded_rule.hpp#L40  
  
The qualities of a percent-encoded string are as follows:  
  
* Stored as a percent-encoded string  
* Stores the decoded size  
* Provides algorithms for decoding  
* Always valid  
  
Final thoughts:  
  
* All algorithms and types which deal with percent-encoding use the prefix "pct"  
* The result of parsing a percent encoded string gives you a percent encoded string  
  
The best suffix for naming this lazy range is a toss-up but I feel like these are the only realistic type name candidates:  
  
    pct_encoded_str  
    pct_encoded_view  
    pct_encoded_range  
  
These all look very similar to me. None are perfect but they are all close enough. I might even be convinced to accept `pct_encoded` with no suffiix if there was sufficient _clamour_.

---

## Review 141 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:06:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040348399  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  41 |+     ```
  42 |+     ]
  43 |+     [`T&`]
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:06:35 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922201231  

you don't need to require that `assign` returns `T&`

> Username: alandefreitas  
> Created_at: 2022-07-15 18:03:45 UTC  
> Updated_at: 2022-07-15 18:03:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922404690  

What should I write as a return type? "Anything"? Removing the column might be problematic if we happen to have other requirements.

> Username: vinniefalco  
> Created_at: 2022-07-15 18:13:33 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922410956  

just leave it empty


---

## Review 142 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:07:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040349274  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  43 |+     [`T&`]
  44 |+     [
  45 |+         This member function assigns the `char`s from the
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:07:14 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922201823  

EWWWWWWW!!!!!!! I'm offended by this. Prefer:  
  
"...assigns the characters from...."


---

## Review 143 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:07:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040349917  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  45 |+         This member function assigns the `char`s from the
  46 |+         range of characters in `[first, last)` to the
  47 |+         container `s`.
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:07:41 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922202248  

You don't need to say container, you can just say `s`, because `s` was defined earlier.


---

## Review 144 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:08:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040351687  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  59 |+         container `s`.
  60 |+     ]
  61 |+ ]]
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:08:57 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922203465  

I think you should also require a nested `value_type` equal to `char`

> Username: alandefreitas  
> Created_at: 2022-07-15 18:07:09 UTC  
> Updated_at: 2022-07-15 18:07:59 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922406834  

I think that's partly implied in "`first`, `last` are iterators whose `value_type` is `char`" because the other value types are also OK, as long as the functions work with char iterators. For instance, a container of `unsigned char` would also work here.   
  
If the conversion from char works in the container, we can assume it's intended. If it doesn't work, then it's implied in "`first`, `last` are iterators whose `value_type` is `char`".

> Username: vinniefalco  
> Created_at: 2022-07-15 18:16:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922413907  

Based on what I have observed with Peter and Dmitry about how they are writing the traits for `json::value_from` and `json::value_to`, they would constrain the type to require a nested `value_type` of `char`. The rationale is that it eliminates false positives. In other words, a more concise set of requirements is preferable to a looser set of requirements.


---

## Review 145 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:09:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040352599  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  67 |+ [heading Models]
  68 |+ 
  69 |+ * [@https://en.cppreference.com/w/cpp/string/basic_string `std::string`]
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:09:37 UTC  
> Updated_at: 2022-07-15 14:09:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922204075  

didn't you already define `__std__string__` as a Quickbook macro?


---

## Review 146 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 14:12:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040356714  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  57 |+         This member function appends the `char`s from the
  58 |+         range of characters in `[first, last)` to the
  59 |+         container `s`.
```

> Username: vinniefalco  
> Created_at: 2022-07-15 14:12:35 UTC  
> Updated_at: 2022-07-15 14:12:49 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922206777  

You could say "The characters in the range `[ first, last )` are appended to `s`."  
  
But.... this is gonna blow your mind... we _actually_ don't need to say anything, because the postconditions are out of scope for the concept. The only requirement is that it is syntactically valid. It is up to the user to decide what semantics they desire. I'm not sure what the "perfect" way of documenting this is.

> Username: alandefreitas  
> Created_at: 2022-07-15 18:10:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922409269  

The column is also about expected Semantics. This is useful even though the user technically able to have different semantics there. The pre- and post-conditions are never part of the concept or are only there as expectations/semantics, because concepts are not able to evaluate that.


---

## Comment 147

> Username: alandefreitas  
> Created_at: 2022-07-15 18:02:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185776936  

About naming, it would probably be useful to get external input here.   
  
@akrzemi1 ? @sehe ?  
  
> I think "decoded" is misleading and incorrect  
  
OK. Your [original comment](https://github.com/CPPAlliance/url/pull/229#issuecomment-1185094251) was about the prefix and suffix only. Your [latest comment](https://github.com/CPPAlliance/url/pull/229#issuecomment-1185578429) adds the decision about encoded/decoded middle name.   
  
Yes. I agree. This is kind of confusing.  
  
TL;DR: Now that I have made some small research on how the standard names these views, I think neither `decoded` nor `encoded` are appropriate. And the standard is right on this one. As you said, the input string is just a regular string to be transformed. So the input hasn't necessarily been neither `decoded` nor `decoded`. Also, views are lazy. So what the class represents is neither `decoded` nor `encoded` because the values haven't been transformed yet.  
  
> I think "decoded" is misleading and incorrect.   
  
About the first `encode`/`decode` distinction first, and considering the points you included below that, I think this inference comes from an over-exaggeration of the similarities between the current `decoded_view` and `pct_encoded_str`. This exaggeration would lead to a name based on what the class stores privately instead of what the class is, does, and represents.  
  
For reference, here's the whole definition of `pct_encoded_str`:  
  
https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/include/boost/url/pct_encoding_types.hpp#L22-L31  
  
In terms of what they store, they are similar, although still not the same. `pct_encoded_str` stores a public string view and a public unsigned integer. `decoded_view` stores a private string view, a private unsigned integer, and a private boolean. That's pretty much all the similarity we have the in the classes that exist at the moment.  
  
Now,  
  
- In terms of history/purpose,   
    - `pct_encoded_str` is just meant to represent the result of parsing a percent-encoded string in the context of the grammar.  
    - `decoded_view` is an independent view class to be returned and represent components in URLs that have already been parsed.  
- In terms of semantics,   
    - `pct_encoded_str` has no independent semantics beyond what it stores.   
        - Its capabilities could be replaced with `pair<string_view, size_t>`.   
        - The member names only make sense in the context of parsing strings we know are percent encoded.   
        - `pct_encoded_str` has no independent meaning outside `urls::grammar`  
            - In an independent context, the name and members would be very misleading.   
            - Nothing in the class forces `str` to have any encoding or `decoded_size` to be what it says it is  
            - The class has no instructions on how to encode/decode it.   
            - Outside `urls::grammar`, `pct_encoded_str` is essentially a `pair<string_view, size_t>` with misleading names.  
        - Most importantly, `pct_encoded_str` does not represent an encoded or a decoded string by itself - it stores any string (on a later point, `decoded_view` doesn't either).   
            - It stores a string and makes no promise to decode anything.   
            - In fact, we just _expect_ the string is not going to be decoded because of the grammar functions. The class does nothing about it.  
    - `decoded_view` has very clear view semantics and can only represent what it says it represents  
        - The class doesn't require the input to be encoded and does not encode the output. It only does the decoding of what it's given.  
        - The current `decoded_view` represents a string with string-decoding applied.   
            - It does that by implementing the requirements of a [view](https://en.cppreference.com/w/cpp/ranges/view) that allows it to be used in range pipelines and all the other view algorithms.  
            - Its iterators return the decoded chars, not the original chars  
            - The few functions that let the user access the private encoded string are equivalent to `container::data()` where the encoded data is always referred to as the _underlying_ data for the other thing the class represents.  
        - All the members are private, and they might even need to change at some point.   
        - The extra information in `decoded_view` is useless in a grammar/parsing result type.    
        - The class does not require and has no idea if the input has any percent-encoding applied. It doesn't deal with grammar. And that's ok because it's outside the context of grammar  
        - The only class knows that any percent encoded input will be always decoded, regardless of the input. It does no encoding to do anything.  
        - Just like `string_view`s are used to represent encoded strings in the library, `decoded_view` stores an encoded string to represent the decoded string but it does not represent an encoded string, which is what `string_view` does.   
  
But considering the standard:  
  
Having said that, neither `decoded`/`encoded` seem appropriate here, because nothing has been `decoded` yet, let alone `encoded`. By looking at the [conventions for views](https://en.cppreference.com/w/cpp/ranges#Range_adaptors) in cppreference, we have two things. The class is an efficient way to represent a combination of an adapted `split_view` (splitting at `%`+2 chars or other chars) and a `transform_view` (transforming subranges into a single char).  
  
In terms of naming views, the function `XXXXX` always returns an `XXXXXX_view`, where `XXXXX` is a _verb_ representing what the view does to the input. An obvious corollary is that classes are always named after what the view does rather than what the input represents because the view simply cannot know. For instance, we have the `views::split` operation to create a `ranges::split_view`. Likewise, the operation we are performing would have the verb `decode` or `pct_decode`, while the view would be a `decode_view` or `pct_decode_view`.  
  
All that convention intuitively makes sense now. How weird would it be if `url_view::encoded_host()` returns a regular `string` / view and then `url_view::host()` is the one to return an `encoded_string` / view (?). Or even a view that receives a "encoded" string as input and promises to represent/return an "encoded" string again as its transformed output (?).  
  
> All algorithms and types which deal with percent-encoding use the prefix "pct"  
  
Yes. So back to the other points, I just removed the prefix because of the reasons above. Mostly, the "less typing" mantra, and because the `pct_` is the only inference possible. But the `pct_` has the advantage of maintaining symmetry with the other functions.   
  
> The best suffix for naming this lazy range is a toss-up but I feel like these are the only realistic type name candidates:  
  
`_view` is definitely better. All views in the standard and other libraries have the `_view` suffix.

---

## Comment 148

> Username: cppalliance-bot  
> Created_at: 2022-07-15 18:19:54 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185789206  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 149

> Username: vinniefalco  
> Created_at: 2022-07-15 18:40:57 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185809954  

The type is first and foremost an encapsulation of a valid percent-encoded string. The fact that it happens to be a view / lazy-decoded range is secondary. Our design choices should be based on its use to represent the percent-encoded string.

---

## Review 150 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 19:26:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040724298  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  13 |+ `append` member functions that accept iterators to `char`.
  14 |+ 
  15 |+ The class __decoded_view__ provides a operations
```

> Username: vinniefalco  
> Created_at: 2022-07-15 19:26:37 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922461046  

are we changing this macro?


---

## Comment 151

> Username: cppalliance-bot  
> Created_at: 2022-07-15 19:29:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185843706  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 152 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 19:53:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040746510  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  15 |+ The class __pct_encoded_view__ provides a operations
  16 |+ to assign and append percent-decoded values to a
  17 |+ ['MutableString].
```

> Username: vinniefalco  
> Created_at: 2022-07-15 19:53:58 UTC  
> Updated_at: 2022-07-15 19:54:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922476851  

Grammar. This could get you started:  
  
Objects of type __pct_encoded_view__ reference URL or other component strings that are percent-encoded. That is, that characters which are special (not in the allowed character set) are represented with escapes that start with a percent followed by a two-digit hexadecimal number of the corresponding character code (which may be part of a UTF-8 code point depending on the context). The primary operations are:  
  
* Comparison to other percent-encoded or plain strings  
* Appending the decoded characters to an existing container  
* Assigning the decoded characters to an existing container  
* Iterating over the represented decoded character range  
* Accessing the underlying percent-encoded character buffer  
...

> Username: vinniefalco  
> Created_at: 2022-07-15 20:11:10 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922486456  

"These objects can only be constructed from strings that have a valid percent-encoding, otherwise construction fails."  
  
This should probably go on the main class javadoc


---

## Comment 153

> Username: cppalliance-bot  
> Created_at: 2022-07-15 19:54:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185861630  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 154 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 20:07:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040757431  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  45 |+     [
  46 |+         This member function assigns the characters from the
  47 |+         range of characters in `[first, last)` to `s`.
```

> Username: vinniefalco  
> Created_at: 2022-07-15 20:07:34 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922484410  

"characters" twice.. lol


---

## Review 155 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 20:08:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040758352  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  32 |+ * `T` is a type meeting the requirements of ['MutableString]
  33 |+ * `s` is a mutable container of type `T`
  34 |+ * `first`, `last` are iterators whose `value_type` is `char`
```

> Username: vinniefalco  
> Created_at: 2022-07-15 20:08:43 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922485018  

`first` and `last` are _InputIterators_ whose `value_type` is `char` and which refer to the valid character sequence `[ first, last )`.


---

## Review 156 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 20:09:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040758753  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  67 |+ 
  68 |+ * __std_string__
  69 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-07-15 20:09:13 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922485273  

"See Also:" `urls::pct_encoded_rule`


---

## Review 157 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 20:11:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040760836  

📁 doc/qbk/6.2.MutableString.qbk

```diff
   8 |+ ]
   9 |+ 
  10 |+ [section:mutablestring MutableString]
```

> Username: vinniefalco  
> Created_at: 2022-07-15 20:11:43 UTC  
> Updated_at: 2022-07-15 20:11:44 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922486720  

the section name defaults to the title in lowercase already


---

## Comment 158

> Username: cppalliance-bot  
> Created_at: 2022-07-15 20:34:53 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1185896480  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 159 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:05:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040801012  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  42 |+     ```
  43 |+     ]
  44 |+     []
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:05:01 UTC  
> Updated_at: 2022-07-15 21:05:02 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922515398  

Well you could put `char` in the second column :)


---

## Review 160 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:05:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040801272  

📁 doc/qbk/6.2.MutableString.qbk

```diff
  44 |+     []
  45 |+     [
  46 |+         `T::value_type` must be `char`.
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:05:23 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922515590  

"Requires: std::is_same_v< T::value_type, char >"


---

## Review 161 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:06:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040801824  

📁 include/boost/url/grammar/is_mutable_string.hpp

```diff
  27 |+ {
  28 |+ template<class T, class = void>
  29 |+ struct has_value_type : std::false_type {};
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:06:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922515958  

I think you could have done this all in `is_mutable_string`

> Username: alandefreitas  
> Created_at: 2022-07-15 22:26:21 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922554347  

I tried to do that but I couldn't because of some other implementation details.


---

## Review 162 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:06:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040802110  

📁 include/boost/url/grammar/is_mutable_string.hpp

```diff
  68 |+         detail::has_assign_and_append<T, I>::value
  69 |+         >::type>
  70 |+     : std::is_same<typename T::value_type, char>
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:06:32 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922516167  

Oh you can do better than this :) do it all in `is_mutable_string`

> Username: alandefreitas  
> Created_at: 2022-07-15 22:27:27 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922554694  

Everything was in `is_mutable_string`, but I didn't manage to include the `value_type` requirement without splitting it.


---

## Review 163 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:07:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040802430  

📁 include/boost/url/grammar/is_mutable_string.hpp

```diff
  24 |+ using is_mutable_string = __see_below__;
  25 |+ #else
  26 |+ namespace detail
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:06:59 UTC  
> Updated_at: 2022-07-15 21:07:00 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922516370  

You shouldn't put detail stuff in headers that docca sees

> Username: alandefreitas  
> Created_at: 2022-07-15 22:28:35 UTC  
> Updated_at: 2022-07-15 22:28:40 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922555052  

I thought the `detail` namespace was excluded in Jamfile. In any case, this is `ifdef`ed out.

> Username: vinniefalco  
> Created_at: 2022-07-15 22:43:23 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922561162  

the detail/ directory is excluded by the jamfile but there's no way to exclude the namespace from being parsed. docca throws them out, but it still has to lexically analyze it. Better that doxygen never sees it, so that the docs generate faster.


---

## Review 164 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:09:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040804545  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  39 |+ 
  40 |+     The underlying encoded data is constant and
  41 |+     is never mutated by the view. Thus, the views
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:09:10 UTC  
> Updated_at: 2022-07-15 21:09:11 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922517686  

constant implies never mutated, you're saying the same thing twice


---

## Review 165 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:10:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040805127  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  40 |+     The underlying encoded data is constant and
  41 |+     is never mutated by the view. Thus, the views
  42 |+     only contain the const-qualified functions
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:10:01 UTC  
> Updated_at: 2022-07-15 21:10:02 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922518089  

You dont need to say that begin and end exist, since they are already in the reference. Generally speaking you should avoid mentioning particular member functions in the class brief unless it is something central to the class. And you dont need to say that they are const qualified, no one cares about that who is reading this brief.


---

## Review 166 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:10:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040805458  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  45 |+     Any operation on the underlying percent-encoded
  46 |+     strings invalidates the view iterators. The view
  47 |+     should be recreated after any operation that
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:10:14 UTC  
> Updated_at: 2022-07-15 21:10:15 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922518276  

This needs to be explained in the iterator javadoc as well


---

## Review 167 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:11:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040806312  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  31 |+     copy. This avoid any unnecessary memory allocations
  32 |+     when working with decoded strings, and transfers
  33 |+     this responsibility to the user.
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:11:26 UTC  
> Updated_at: 2022-07-15 21:12:22 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922518945  

I think it would be better to just say that "Storage for the underlying character buffer is not transferred; the caller is responsible for ensuring that the lifetime of the buffer lasts until the view is destroyed."  
  
In terms of complexity ("inexpensive") you should have "@par Complexity" clauses on the member functions and in the iterator javadoc rather than trying to explain it here.


---

## Review 168 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:13:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040807390  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  23 |+ /** A view of the percent decoded characters in a string
  24 |+ 
  25 |+     This class is a lightweight wrapper that presents
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:13:05 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922519690  

Instead of all this lightweight wrapper business you could just say  
  
Objects of type pct_encoded_view reference URL or other component strings that are percent-encoded. That is, that characters which are special (not in the allowed character set) are represented with escapes that start with a percent followed by a two-digit hexadecimal number of the corresponding character code (which may be part of a UTF-8 code point depending on the context). The primary operations are:  
  
* Comparison to other percent-encoded or plain strings  
* Appending the decoded characters to an existing container  
* Assigning the decoded characters to an existing container  
* Iterating over the represented decoded character range  
* Accessing the underlying percent-encoded character buffer  
...


---

## Review 169 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 21:14:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040808707  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  30 |+     character buffer, are inexpensive to create and
  31 |+     copy. This avoid any unnecessary memory allocations
  32 |+     when working with decoded strings, and transfers
```

> Username: vinniefalco  
> Created_at: 2022-07-15 21:14:49 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922520563  

You can say "The caller must opt-in to perform expensive, possibly-allocating decoding operations which produce a contiguous character buffer by calling @ref append_to, @ref assign_to,  or @ref to_string."


---

## Review 170 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-15 22:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/229#pullrequestreview-1040849754  

📁 test/unit/snippets.cpp

```diff
 936 |+ {
 937 |+     template< class InputIt >
 938 |+     MutableString& assign( InputIt first, InputIt last );
```

> Username: vinniefalco  
> Created_at: 2022-07-15 22:13:49 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922548677  

You don't need the requirement that the function return `T&`

> Username: alandefreitas  
> Created_at: 2022-07-15 22:35:50 UTC  
> Url: https://github.com/boostorg/url/pull/229#discussion_r922558196  

I replaced `MutableString&` with `void`. But that would imply the requirement is `void`. Both are wrong, but `MutableString&` is more common although void might be safer (? not sure).


---

## Comment 171

> Username: cppalliance-bot  
> Created_at: 2022-07-15 22:44:52 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1186004134  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 172

> Username: sehe  
> Created_at: 2022-07-15 23:58:08 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1186029543  

> In fact there is no such thing as a "percent-decoded string" - this is just a plain string ([here](https://github.com/CPPAlliance/url/pull/229#issuecomment-1185578429))  
  
You could also say there is no plain string (there's only a view over pct-encoded storage). Again, I think "[pct_]decoding_view" would be clear? UPDATE `decode_view` works as well (HT @alandefreitas)  
  
W.r.t. naming, I agree that it's never going to read like poetry, and that's probably fine. I suppose `_view` suffixed will go over better with reviewers.

---

## Comment 173

> Username: cppalliance-bot  
> Created_at: 2022-07-16 01:19:55 UTC  
> Url: https://github.com/boostorg/url/pull/229#issuecomment-1186047993  

An automated preview of the documentation is available at [https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://229.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
