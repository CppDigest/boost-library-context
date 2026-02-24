# #57 Improve string exposition [Closed]

> Username: sdkrystian  
> Created at: 2020-03-13 00:13:26 UTC  
> Updated at: 2020-06-10 15:20:29 UTC  
> Closed at: 2020-03-14 20:35:43 UTC  
> Url: https://github.com/boostorg/json/pull/57  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:34:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982011  

📁 doc/qbk/02_2_strings.qbk

```diff
  17 |- operate the same way, with these variations:
  16 |+ __string__.
  17 |+ These strings have an modified `string_view` based version of the `std::string` interface, and
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:34:14 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973389  

"an" -> "a"

> Username: vinniefalco  
> Created_at: 2020-03-13 00:34:24 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973409  

Perhaps `string_view` should be a link to cppreference


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:34:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982149  

📁 doc/qbk/02_2_strings.qbk

```diff
  24 |+ * redundant overloads for string operations have been replaced with a `string_view` based interface,
  25 |+ 
  26 |+ * access to characters in the range `[size(), capacity()]` is permitted, and
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:34:54 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973497  

This should be a half-open interval `[ ... )`

> Username: sdkrystian  
> Created_at: 2020-03-13 20:34:49 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392457751  

Fixed.

> Username: mloskot  
> Created_at: 2020-03-13 21:03:45 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392474964  

(off-topic) I'm new here, what is the workflow?  
- @vinniefalco requests the change  
- @sdkrystian makes the change  
- who hits "Resolve conversation"? @sdkrystian after fixing or @vinniefalco after verifying it's been fixed?

> Username: vinniefalco  
> Created_at: 2020-03-13 21:31:08 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392491640  

I don't use any of that I just leave individual comments.

> Username: mloskot  
> Created_at: 2020-03-13 21:47:01 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392500615  

All are individual comments but if thread is not resolved, not closed, it's dangling post PR update and confusing reviews, what's been addressed.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:35:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982402  

📁 doc/qbk/02_2_strings.qbk

```diff
  23 |- * New characters are 
  30 |+ With the `string_view` based interface, operations requiring an input string are implemented as a single overload with
  31 |+ a parameter of type `string_view`, and can accept most string-like objects in addition to other `string_view`s.
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:35:51 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973752  

You're using the word string and string_view way too many times it is like "string string and string string view string the string view the viewed string view stringed view" - blah


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:36:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982538  

📁 doc/qbk/02_2_strings.qbk

```diff
  40 |+ * a `std::string` parameter, or
  41 |+ 
  42 |+ * a `std::string` parameter and two `size_type` parameters that specify a substring within that string, or
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:36:20 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973907  

"string" three times in a sentence

> Username: sdkrystian  
> Created_at: 2020-03-13 20:34:41 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392457665  

That's the formal spec of what actually happens, so it has to be redundant.

> Username: mloskot  
> Created_at: 2020-03-13 21:12:56 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392480493  

Here is my quick try for the whole section  
  
```  
More formally, member function overloads for `std::string` accept either of the following parameter sets:  
  
* object of `std::string` type  
  
* object of `std::string` type and two `size_type` parameters that specify position of the first character and length of the substring in the object  
  
* object of type convertible to __string_view__  
  
* object of type convertible to __string_view__ and two `size_type` parameters that specify position of the first character and length of the substring in the object  
  
* `const_pointer` to null-terminated character string  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:36:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982615  

📁 doc/qbk/02_2_strings.qbk

```diff
  58 |+ [snippet_strings_3]
  59 |+ 
  60 |+ With the removal of overloads that specify parameters for a substring, a member function `subview` that returns a `string_view` referring to
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:36:39 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391973988  

60 cols max please


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:37:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373982711  

📁 doc/qbk/02_2_strings.qbk

```diff
  59 |+ 
  60 |+ With the removal of overloads that specify parameters for a substring, a member function `subview` that returns a `string_view` referring to
  61 |+ a substring within the __string__ is provided to facilitate cheap substring operations:
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:37:07 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391974071  

string...within...string...to..substring the string view thing string viewed string string TOO MANY STRINGS!! ARGH

> Username: sdkrystian  
> Created_at: 2020-03-13 20:34:12 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392457386  

You're right, it's a little excess. I trimmed that line down.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:39:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373983267  

📁 doc/qbk/02_2_strings.qbk

```diff
  63 |+ [snippet_strings_2]
  64 |+ 
  65 |+ A __string__ may be constructed using the default storage without incurrening any
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:39:24 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391974567  

"incurrening"

> Username: sdkrystian  
> Created_at: 2020-03-13 20:33:44 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392457121  

Fixed.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:40:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373983507  

📁 test/snippets.cpp

```diff
  78 |+ 
  79 |+         // all of these call compare(string_view)
  80 |+         str.compare(sv);
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:40:19 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391974774  

make these `assert`s (not `BOOST_ASSERT`), e.g.:  
```  
assert( str.compare(sv) < 0 );  
```

> Username: sdkrystian  
> Created_at: 2020-03-13 20:33:38 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392457073  

For the other examples specifying the post-operation state of the string I added asserts. No asserts are needed here though.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:42:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373984158  

📁 test/snippets.cpp

```diff
  90 |+             operator string_view()
  91 |+             {
  92 |+                 return "Sorta stringish";
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:42:58 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391975369  

This wording needs to be sterile


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:43:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373984165  

📁 test/snippets.cpp

```diff
  86 |+         str.compare("Boost");
  87 |+ 
  88 |+         struct string_viewish
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:43:01 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391975379  

This wording needs to be sterile


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-13 00:45:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-373984753  

📁 test/snippets.cpp

```diff
  95 |+ 
  96 |+         // even for this
  97 |+         str.compare(string_viewish());
```

> Username: vinniefalco  
> Created_at: 2020-03-13 00:45:12 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r391975891  

Do we really need to show this in the examples? All it shows is that C++ conversion operator works. It doesn't add any information about the library. And it will probably confuse some folks.

> Username: sdkrystian  
> Created_at: 2020-03-13 20:32:46 UTC  
> Updated_at: 2020-03-14 15:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392456515  

Alright, removed that


---

## Comment 12

> Username: vinniefalco  
> Created_at: 2020-03-14 20:35:43 UTC  
> Url: https://github.com/boostorg/json/pull/57#issuecomment-599131948  

Merged, thanks!

---

## Review 13 [Commented]

> Username: mloskot  
> Created_at: 2020-03-14 20:48:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/57#pullrequestreview-374753115  

📁 doc/qbk/02_2_strings.qbk

```diff
  43 |+ * a `std::string` parameter, or
  44 |+ 
  45 |+ * a `std::string` parameter and two `size_type` parameters that specify a substring, or
```

> Username: mloskot  
> Created_at: 2020-03-14 20:48:56 UTC  
> Updated_at: 2020-03-14 20:50:01 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392618146  

> two `size_type` parameters that specify a substring  
  
@vinniefalco Two numbers can specify substring or range of string in at least two different ways. I don't know how anal the docs should be about that in your opinion, but less room left for interpretation or questions the better, IMO.

> Username: vinniefalco  
> Created_at: 2020-03-14 20:55:57 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392618571  

The docs are a work in progress, I imagine that we will continue to keep improving them over many pull requests. @sdkrystian

> Username: mloskot  
> Created_at: 2020-03-14 21:00:32 UTC  
> Updated_at: 2020-03-14 21:00:33 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392618872  

@vinniefalco Understood. I just picked and followed the nitpicking approach.

> Username: sdkrystian  
> Created_at: 2020-03-14 21:03:50 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392619064  

@mloskot  There's a careful balance between being pedantic and not boring the user. I could write these in standardese, but that would bore the user. Besides, this specification doesn't need to be specific as it outlines the overloads that are removed, so exact behavior is not needed.

> Username: mloskot  
> Created_at: 2020-03-14 21:10:08 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392619456  

@sdkrystian Yes, the balance is important but AFAIU that part is "formal spec" where such balance does not quite apply (as it doesn't in API reference either). Anyway, I'm being anal and I expect a [function spec to be boringly precise](https://en.cppreference.com/w/cpp/string/basic_string/substr). Anyway, my question has been answered. Thx.

> Username: vinniefalco  
> Created_at: 2020-03-15 00:57:21 UTC  
> Updated_at: 2020-03-15 00:57:22 UTC  
> Url: https://github.com/boostorg/json/pull/57#discussion_r392631701  

The place for boring precision is in the javadoc. The exposition should be concise, elegant, and inspiring.


---
