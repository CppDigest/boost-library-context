# #570 X3: constexprize parsers constructors [Merged]

> Username: wanghan02  
> Created at: 2020-03-02 11:50:10 UTC  
> Updated at: 2020-03-04 17:08:12 UTC  
> Merged at: 2020-03-04 15:18:03 UTC  
> Closed at: 2020-03-04 15:18:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/570  

Constexpr constructor and constexpr factory function could save valuable execution time. Especially for local parsers, whose construction time could take up to 40% of total execution time in release build and up to 90% in debug build.  
  
1. Some x3 parsers's constructor could easily be constexpr.   
2. unary_parser and binary_parser and all their derived classes cannot directly be constexpr because we introduced Static Initialization Order Fiasco (SIOF) checking (not constexpr) in their constructors in pull request [229](https://github.com/boostorg/spirit/pull/229). I propose that users could have an option to disable this SIOF checking by defining a macro `BOOST_SPIRIT_X3_DISABLE_SIOF_CHECK`. Because  
  
    1) It does not check release build.  
    2) The coding style that triggers SIOF is forbidden in many groups. This checking does not bring any benefit in such groups.  
  
Summary: users could use constexpr parsers (except a few ones, e.g. x3::symbols_parser) by defining a macro `BOOST_SPIRIT_X3_DISABLE_SIOF_CHECK` before including x3 headers. This macro will disable SIOF checking. Unit tests are added.

---

## Review 1 [Changes requested]

> Username: Kojoley  
> Created_at: 2020-03-02 14:53:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/spirit/pull/570#pullrequestreview-367258837  

Thanks for working on this. My previous words are still valid, I do not think macro stuff is needed here, we just simply need `what` to be marked as `constexpr` but it should not be really constexpr function.  
  
The way it could be solved is to make return type dependent on the template parameter. I hoped actually just `type_identity_t<std::string>` will do the thing, but either Clang rejects a valid code or GCC/MSVC accepts invalid.  
  
```  
namespace detail {  
  
template <typename T, typename...>  
struct dependent_type_identity { using type = T; };  
  
template <typename T, typename... D>  
using dependent_type_identity_t = typename dependent_type_identity<T, D...>::type;  
  
}  
  
template <typename Parser>  
constexpr detail::dependent_type_identity_t<std::string, Parser> what(Parser const& p)  
{  
    return get_info<Parser>()(p);  
}  
```

---

## Comment 2

> Username: wanghan02  
> Created_at: 2020-03-02 16:32:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-593491561  

> we just simply need what to be marked as constexpr but it should not be really constexpr function.  
  
Thank you for your review. But I still have doubts about this change request.   
  
1. Even if we make `what(p)` a `constexpr` function (It may be doable because it's a template function, `constexpr` template function only requires at least 1 possible specialization is `constexpr`), it does not mean its specializations for each derived parsers are `constexpr` functions. The fact is they are not and they cannot have a `constexpr` constructor.  
  
2. This SIOF checking is done by checking if `x3::rule::name` is `0` initialized via calling the constructor of `std::string`. Whether `x3::rule::name` is `0` initialized is almost certainly not a compile time result. While `constexpr` function (or `constexpr` template function specialization) requires that all its content could be evaluated at compile time. Otherwise it's not a `constexpr` (compile time evaluated) function.  
  
In my opinion, the SIOF checking and `constexpr` are mutually exclusive. The pull request doesn't change existing application code. Users who want to choose `constexpr` parsers over SIOF could simply enable the macro.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-03-02 18:17:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-593542662  

I am strongly against introducing a new undocumented combinatory explosive switch. I have opened #571, it should solve both issues in a most convenient way.

---

## Comment 4

> Username: djowel  
> Created_at: 2020-03-02 21:41:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-593637751  

> I am strongly against introducing a new undocumented combinatory explosive switch. I have opened #571, it should solve both issues in a most convenient way.  
  
I am with @Kojoley on this one. But having said that, the move to constexpr is indeed a step in the right direction!

---

## Comment 5

> Username: wanghan02  
> Created_at: 2020-03-03 15:05:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-593997115  

I have updated the commits based on #571 . Now it's simply constexpr everywhere.

---

## Review 6 [Commented]

> Username: Kojoley  
> Created_at: 2020-03-03 21:41:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/570#pullrequestreview-368307421  

The test you have provided clearly covers less things than the PR touches though I do not think the test is even required.

📁 include/boost/spirit/home/x3/nonterminal/rule.hpp

```diff
  89 | #else
  90 |-         rule() : name("unnamed") {}
  90 |+         constexpr rule() : name("unnamed") {}
```

> Username: Kojoley  
> Created_at: 2020-03-03 20:40:09 UTC  
> Updated_at: 2020-03-03 22:21:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387280339  

This make the macro to be an API changing and I do not like that, but since the default branch is less restrictive I think we can accept it.


📁 include/boost/spirit/home/x3/operator/alternative.hpp

```diff
  44 |     template <typename Left, typename Right>
  45 |-     inline alternative<
  45 |+     inline constexpr alternative<
```

> Username: Kojoley  
> Created_at: 2020-03-03 20:41:26 UTC  
> Updated_at: 2020-03-03 22:21:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387280975  

The `inline` is not needed since `constexpr` implies it on functions.

> Username: wanghan02  
> Created_at: 2020-03-03 22:25:27 UTC  
> Updated_at: 2020-03-03 22:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387331988  

fixed in new commit [667f90f](https://github.com/boostorg/spirit/pull/570/commits/667f90f03a3c0bee9ce639f7c5cea392220331db)


📁 test/x3/constexpr.cpp

```diff
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ 
   9 |+ #define BOOST_SPIRIT_X3_DISABLE_SIOF_CHECK
```

> Username: Kojoley  
> Created_at: 2020-03-03 21:38:40 UTC  
> Updated_at: 2020-03-03 22:21:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387308948  

This is left by omission, I guess.

> Username: wanghan02  
> Created_at: 2020-03-03 22:26:38 UTC  
> Updated_at: 2020-03-03 22:26:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387332518  

fixed in new commit [b7fc3bf](https://github.com/boostorg/spirit/pull/570/commits/b7fc3bf2eb0b5cfab6a79b9f07ac7930fba131ba)


---

## Review 7 [Commented]

> Username: Kojoley  
> Created_at: 2020-03-03 21:59:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/570#pullrequestreview-368356697  

📁 include/boost/spirit/home/x3/char/char_class.hpp

```diff
  73 |     typedef char_class<char_encoding::encoding, name##_tag> name##_type;           \
  74 |-     name##_type const name = name##_type();                                        \
  74 |+     constexpr name##_type name = name##_type();                                        \
```

> Username: Kojoley  
> Created_at: 2020-03-03 21:59:27 UTC  
> Updated_at: 2020-03-03 22:21:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387319475  

Since you have denied edits to your branch, please fix backslashes indentation.

> Username: wanghan02  
> Created_at: 2020-03-03 22:27:29 UTC  
> Updated_at: 2020-03-03 22:27:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#discussion_r387332899  

fixed in new commit [ad88408](https://github.com/boostorg/spirit/pull/570/commits/ad88408b4571ffb0144589c2cefa3f6ae68f4144)


---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-03-04 15:18:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-594593130  

Thanks @wanghan02!

---

## Comment 9

> Username: djowel  
> Created_at: 2020-03-04 17:08:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/570#issuecomment-594667906  

> Thanks @wanghan02!  
  
I second that. Thanks!

---
