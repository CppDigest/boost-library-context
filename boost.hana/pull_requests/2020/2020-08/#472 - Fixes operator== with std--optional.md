# #472 Fixes operator== with std::optional [Merged]

> Username: ricejasonf  
> Created at: 2020-08-30 00:21:48 UTC  
> Updated at: 2020-09-03 15:44:36 UTC  
> Merged at: 2020-09-03 15:38:28 UTC  
> Closed at: 2020-09-03 15:38:28 UTC  
> Url: https://github.com/boostorg/hana/pull/472  

- Prevents comparable operators from considering the tag types like tuple_tag  
    - Allows Struct classes to implement their own comparable operators so it can  
      be used with types like std::optional

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2020-08-31 04:55:47 UTC  
> Url: https://github.com/boostorg/hana/pull/472#issuecomment-683548300  

I removed the tabs. I'm not sure about what I can do for  
  
```  
..\test\issues\github_460.cpp(26): fatal error C1001: An internal error has occurred in the compiler.  
(compiler file 'msc1.cpp', line 1518)  
 To work around this problem, try simplifying or changing the program near the locations listed above.  
Please choose the Technical Support command on the Visual C++  
```  
  
:scream_cat:

---

## Review 2 [Changes requested]

> Username: ldionne  
> Created_at: 2020-09-02 19:16:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/472#pullrequestreview-481117342  

Thanks a lot for working on that, Jason. It's a tricky issue.  
  
I went ahead and tried removing the root cause of this issue, which is that `tag_of` is idempotent by default. As I was fixing errors and errors, it came back to me that Hana tags were *by design* an extension of the type of the object, and so for example that's why `hana::equal` is supposed to work out of the box on most user-defined types. So in addition to it being a major break, I think it would go against the spirit of the library to make `hana::tag_of` non-idempotent by default.  
  
I think we need to search for a solution in the way we're providing comparison operators by default, as you did. I have a few comments about details, but the approach looks good to me.

📁 test/issues/github_460.cpp

```diff
   3 |+ // (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)
   4 |+ 
   5 |+ #include <boost/hana/equal.hpp>
```

> Username: ldionne  
> Created_at: 2020-09-02 18:03:07 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482263831  

Nit: Please sort the includes.

---

📁 test/issues/github_460.cpp

```diff
  15 |+ 
  16 |+     constexpr bool operator == (SomeStruct const& other) {
  17 |+         return hana::equal(hana::members(*this), hana::members(other));
```

> Username: ldionne  
> Created_at: 2020-09-02 18:03:39 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482264359  

Nit: `operator==(SomeStruct ...` (no whitespace)


📁 include/boost/hana/equal.hpp

```diff
  94 |+     struct equal_impl<T, U, when<
  95 |+         detail::EqualityComparable<T, U>::value &&
  96 |+         !(Struct<T>::value && Struct<U>::value)
```

> Username: ldionne  
> Created_at: 2020-09-02 18:14:12 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482270021  

I'm tempted to think that the right fix for the Struct equality ambiguity is to instead not define `equal_impl` for structs that already have an `operator==`. So, instead of this:  
  
```  
template <typename S>  
struct equal_impl<S, S, when<hana::Struct<S>::value && !hana::detail::EqualityComparable<S, S>::value>>;  
```  
  
The problem here is that `S` is a tag, so we don't know whether we can actually use `operator==` on the object the tag came from. But in most cases, I think `Struct`s don't have a custom `hana::tag_of` anyway, so this might not be an issue. Any thoughts?

> Username: ricejasonf  
> Created_at: 2020-09-02 19:50:35 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482368380  

I agree that my solution was a little ad hoc :adhesive_bandage:. Your suggestion means that the user could not call `hana::equal` in their `operator==`, but the test demonstrates that it is trivial to workaround.


📁 include/boost/hana/detail/operators/comparable.hpp

```diff
  29 |-             detail::comparable_operators<typename hana::tag_of<X>::type>::value ||
  30 |-             detail::comparable_operators<typename hana::tag_of<Y>::type>::value
  41 |+             detail::comparable_operators_sfinae<X>::value ||
```

> Username: ldionne  
> Created_at: 2020-09-02 19:15:09 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482325181  

How about:  
  
```  
namespace detail {  
  template <typename T>  
  struct has_idempotent_tag  
    : std::is_same<hana::tag_of_t<T>, std::remove_const_t<std::remove_reference_t<T>>>  
  { };  
}  
```  
  
And then  
  
```  
std::enable_if_t<(!detail::has_idempotent_tag<X>::value && detail::comparable_operators<X>::value) ||  ...>  
```  
  
If we want to be lazy, we can define (or perhaps we already have somewhere) a lazy `and_` type trait. WDYT?

> Username: ricejasonf  
> Created_at: 2020-09-02 19:53:29 UTC  
> Updated_at: 2020-09-02 23:01:00 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482372269  

I'm not sure what you mean by "lazy". Do you mean short-circuiting the template instantiations?

> Username: ricejasonf  
> Created_at: 2020-09-02 23:06:31 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r482583734  

I used your metafunction, but the logic was a bit different. Neither of the operands could be a tag, or for some reason it compiled and always returned false because it was finding the default impl. (I think)


---

## Review 3 [Commented]

> Username: ldionne  
> Created_at: 2020-09-03 15:34:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/472#pullrequestreview-481961793  

📁 include/boost/hana/equal.hpp

```diff
 194 |+     struct equal_impl<S, S, when<
 195 |+         hana::Struct<S>::value &&
 196 |+         !detail::EqualityComparable<S, S>::value
```

> Username: ldionne  
> Created_at: 2020-09-03 15:34:15 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r483070665  

Can you please add a test for that thing specifically? You could add it to `test/concept/struct/equal.cpp`.

> Username: ldionne  
> Created_at: 2020-09-03 15:37:57 UTC  
> Url: https://github.com/boostorg/hana/pull/472#discussion_r483073180  

Actually, nevermind that. You already have one test for that in `github_460.cpp`.


---

## Comment 4

> Username: ldionne  
> Created_at: 2020-09-03 15:38:37 UTC  
> Url: https://github.com/boostorg/hana/pull/472#issuecomment-686577336  

I think I'm happy with this. Thanks a lot for the work!

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2020-09-03 15:44:36 UTC  
> Url: https://github.com/boostorg/hana/pull/472#issuecomment-686580894  

What about the Visual C++ bug?

---
