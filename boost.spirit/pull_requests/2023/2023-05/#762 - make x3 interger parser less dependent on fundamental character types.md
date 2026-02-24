# #762 make x3 interger parser less dependent on fundamental character types [Closed]

> Username: wanghan02  
> Created at: 2023-05-05 10:49:34 UTC  
> Updated at: 2025-05-09 08:46:10 UTC  
> Closed at: 2025-05-09 08:46:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/762  

Many of the spirit x3 parsers are natualy able to work with user defined char types. It would be very helpful if x3's integer parser could be one of those parsers. And it could be done by more canonical implementation of `radix_traits::is_valid` and `radix_traits::digit`.  
  
In `radix_traits::is_valid`, input character `ch` of type `Char` is compared with several ascii characters of type `char`. The `static_cast` destination type should still be ascii character type `char`, not the input character type `Char`. Comparing input character `ch` with ascii type `char` should be the more canonical way to do the range check just like the comparison between `ch` with other ascii `char` literals (`'0', '9', 'a', 'A'`). If we have a user defined character type which doesn't support implicit conversion to/from the promoted integer type, the range check still works!  
  
In the old implementation of `radix_traits::digit`, the `Radix` check is not necessary and incomplete.   
  
```  
            return (Radix <= 10 || (ch >= '0' && ch <= '9'))  
                ? ch - '0'  
                : char_encoding::ascii::tolower(ch) - 'a' + 10;  
```  
  
Since `radix_traits::digit` is only called after the range check `radix_traits::is_valid`, we don't have to do any range check at all in `radix_traits::digit`. The input character `ch` is guaranteed to be in one of the 3 ranges: `'0'`-`'9'`, `'a'` - `'a' + Radix -10 -1` and `'A'` - `'A' + Radix -10 -1`. We also don't have to call `char_encoding::ascii::tolower(ch)` which adds another layer of unnecessary dependency. Given the valid range is right above, a simpler implementation as below is clear and efficient. And it doesn't require implicit conversion to integer type from input character type as well!  
  
```  
            return ch < 'A'  
                ? ch - '0'  
                : ch < 'a'  
                    ? ch - 'A' + 10  
                    : ch - 'a' + 10;  
```  
  
Both old and new implementations assume char literal uses ascii table.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2023-05-06 13:28:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/762#pullrequestreview-1415775616  

> user defined char types.  
  
I don't think I ever seen one. It would help me in understanding the intent of the change if you show one.  
  
Please also notice that when a patch doesn't add tests there is no guarantee that your use case will not be broken in a future by some unrelated change.

📁 include/boost/spirit/home/x3/support/numeric_utils/detail/extract_int.hpp

```diff
 115 |+             return (ch >= '0' && ch <= (Radix > 10 ? '9' : static_cast<char>('0' + Radix -1)))
 116 |+                 || (Radix > 10 && ch >= 'a' && ch <= static_cast<char>('a' + Radix -10 -1))
 117 |+                 || (Radix > 10 && ch >= 'A' && ch <= static_cast<char>('A' + Radix -10 -1));
```

> Username: Kojoley  
> Created_at: 2023-05-06 13:15:49 UTC  
> Updated_at: 2023-05-06 13:28:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1186696518  

I agree that at least for consistency it is better, though the original code is relying without a static assert or a comment on a fact that all these constants always are positive numbers and radix addition to a constant would never overflow or will be caught by the compiler.

---

📁 include/boost/spirit/home/x3/support/numeric_utils/detail/extract_int.hpp

```diff
 121 |         inline static unsigned digit(Char ch)
 122 |         {
 123 |-             return (Radix <= 10 || (ch >= '0' && ch <= '9'))
```

> Username: Kojoley  
> Created_at: 2023-05-06 13:16:37 UTC  
> Updated_at: 2023-05-06 13:28:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1186696622  

It is a compile time branch, an `if constexpr` that doesn't produce a range of compiler warnings https://github.com/boostorg/spirit/commit/26356b5361e28d1d62cb2dbd7093543ed7f0a832 https://github.com/think-cell/spirit/commit/7771d20cc76562ce645b0f68edd529f91022a42d. You basically replaced a compile time branch with a runtime one.

> Username: wanghan02  
> Created_at: 2023-05-08 07:18:06 UTC  
> Updated_at: 2023-05-08 07:18:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1187108806  

I'm not sure if this minor optimization is better than simplicity. But If it is really important that the commit won't change the performance in any branch, we could add this compile time optimization back in. Then we could keep this optimization and don't rely on char_encoding::ascii::tolower.  
  
```  
     return (Radix <= 10 || ch < 'A')  
        ? ch - '0'  
        : ch < 'a'  
            ? ch - 'A' + 10  
            : ch - 'a' + 10;  
```

> Username: wanghan02  
> Created_at: 2023-05-08 08:05:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1187152610  

We have a [`char_ascii`](https://github.com/think-cell/think-cell-library/blob/main/tc/string/value_restrictive.h) type that checks ascii range during construction. It is implicitly convertible and comparable to fundamental char types but not integer types.  
  
IMO the two changes (with Radix constexpr optimization for the 2nd one?) are better implementation than the original code. They provide better semantics and less dependency. If you @Kojoley agree with both changes I could add a simplified char_ascii type and test cases to cover this change.

> Username: Kojoley  
> Created_at: 2023-05-08 20:05:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1187844632  

Does your 'user defined char type' support bitwise operators? Will replacing `char_encoding::ascii::tolowe` with `(ch | 0x20) - 'a' + 10` work for you?

> Username: wanghan02  
> Created_at: 2023-05-09 09:46:26 UTC  
> Updated_at: 2023-05-09 15:53:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1188399710  

For fundamental char types, integral promotion is performed before doing bitwise operations. Basically it is an integer operation and relies on implicit conversion to integer type. For our `char_ascii` type, we focus more on character side of the type and don't support any integer operations other than getting the distance between 2 `char_ascii`s.  
  
But I do see there are some performance improvement with `(ch | 0x20) - 'a' + 10` than ternary operator in case of fundamental character types. Can we take `std::is_convertible<Char const&, int>::value` into the path to make user-defined char types similar to our `char_ascii` work without downgrade the performance of fundamental char types?  
  
```  
     return (Radix <= 10 || ch < 'A')  
        ? ch - '0'  
        : std::is_convertible<Char const&, int>::value  
            ? (ch | 0x20) - 'a' + 10  
            : ch < 'a'  
                ? ch - 'A' + 10  
                : ch - 'a' + 10;  
```  
  
The performance is the same as below with fundamental char types.  
  
```  
    return (Radix <= 10 || ch < 'A')  
        ? ch - '0'  
        : (ch | 0x20) - 'a' + 10;  
```

> Username: Kojoley  
> Created_at: 2023-05-09 22:16:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1189181772  

I think your char type design is just broken. Is there `tolower` associated with it?  
  
> Can we take `std::is_convertible<Char const&, int>::value` into the path to make user-defined char types similar to our `char_ascii` work without downgrade the performance of fundamental char types?  
  
I don't want to add a code path that literally nobody else is using and also is not tested by the test suite.

> Username: wanghan02  
> Created_at: 2023-05-10 08:39:52 UTC  
> Updated_at: 2023-05-10 12:26:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#discussion_r1189554325  

We simply don't use `tolower`. It doesn't matter how we design our character type. The user has freedom to define any character type. It's just if some day the community wants to add constraints to every spirit x3 parser, what is the minimum request on the `Char` type for this parser? I don't see depending on `tolower` or implicit cast to int is necessary in this function performance-wise and semantics-wise. We can split the function into overloads for better semantics. The `Radix<=10` compile time optimization mixed in the code path is already weird.     
  
```  
        template <typename Char, unsigned Radix_=Radix, std::enable_if_t<Radix_<=10>* = nullptr>  
        inline static unsigned digit(Char ch)  
        {  
            return ch - '0';  
        }  
          
        template <typename Char, unsigned Radix_=Radix, std::enable_if_t<10<Radix_ && std::is_convertible<Char, int>::value>* = nullptr>  
        inline static unsigned digit(Char ch)  
        {  
            return ch < 'A'  
                ? ch - '0'  
                : (ch | 0x20) - 'a' + 10;  
        }  
          
        template <typename Char, unsigned Radix_=Radix, std::enable_if_t<10<Radix_ && !std::is_convertible<Char, int>::value>* = nullptr>  
        inline static unsigned digit(Char ch)  
        {  
            return ch < 'A'  
                ? ch - '0'  
                : ch < 'a'  
                    ? ch - 'A' + 10  
                    : ch - 'a' + 10;  
        }  
```  
  
You can take this pull request as a refactoring suggestion. The first change `static_cast<Char>` -> `static_cast<char>` for me definitely makes the code cleaner. The second change makes `digit` more aligned with the implementation of `is_valid` which doesn't use `tolower` either. Since `digit` is only called if `is_valid` is `true`, the implementation of these 2 functions should be aligned with each other.  But it's only a refactoring suggestion. As I said if you agree, I could add test cases to cover the change. Otherwise we have to specialize this struct to workaround the dependency.


---

## Comment 2

> Username: saki7  
> Created_at: 2025-05-09 08:46:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/762#issuecomment-2865699254  

Closing this as stale. Kojoley have expressed several concerns on the design (including the concept of "user-defined char types"), but not all issues had been resolved or answered by the author since then.

---
