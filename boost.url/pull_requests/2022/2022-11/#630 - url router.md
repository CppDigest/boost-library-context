# #630 url router [Merged]

> Username: alandefreitas  
> Created at: 2022-11-23 18:57:51 UTC  
> Updated at: 2023-01-21 00:44:16 UTC  
> Merged at: 2023-01-21 00:44:16 UTC  
> Closed at: 2023-01-21 00:44:16 UTC  
> Url: https://github.com/boostorg/url/pull/630  

We can use this draft to discuss the design. Countless possibilities here.  
  
Review of other routers: https://gist.github.com/alandefreitas/4557b118fd2a054126e3422ca370f7a2

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-23 19:09:35 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1325541255  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-11-25 02:38:50 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1326962895  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-11-25 02:45:14 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1326965497  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-11-26 03:13:23 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1327969311  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:00:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194896418  

📁 include/boost/url/decode_view.hpp

```diff
 357 |+     BOOST_URL_DECL
 358 |+     bool
 359 |+     starts_with( string_view sv ) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:00:08 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032816304  

`s`


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:01:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194896510  

📁 include/boost/url/experimental/impl/router.hpp

```diff
  16 |+ template <class T>
  17 |+ void
  18 |+ router<T>::route(string_view path, T const& resource)
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:01:31 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032816431  

This algorithm has to be an ordinary function (non template)

> Username: alandefreitas  
> Created_at: 2022-11-26 18:34:44 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032819604  

```cpp  
template <class T>  
void  
router<T>::route(string_view path);  
```  
  
?

> Username: vinniefalco  
> Created_at: 2022-11-27 04:05:48 UTC  
> Updated_at: 2022-11-27 04:05:49 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032863432  

The algorithm should not depend on T


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:02:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194896589  

📁 include/boost/url/experimental/impl/router.hpp

```diff
 110 |+ template <class T>
 111 |+ auto
 112 |+ router<T>::match(string_view request)
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:02:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032816531  

`s`  
  
Should this be a `pct_string_view`?


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-11-26 18:02:58 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1328089638  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:03:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194896680  

📁 include/boost/url/experimental/router.hpp

```diff
  70 |+     public:
  71 |+         explicit
  72 |+         match_results(node const& leaf_)
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:03:42 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032816651  

`node` is a public type?


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:09:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194897122  

📁 include/boost/url/decode_view.hpp

```diff
 374 |+     BOOST_URL_DECL
 375 |+     bool
 376 |+     ends_with( string_view sv ) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:09:51 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032817229  

I'm not excited about adding more stuff to `decode_view`

> Username: alandefreitas  
> Created_at: 2022-11-26 18:38:31 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032819922  

I guess we could write some free functions. Matching decoded segments without these functions is nearly impossible.

> Username: vinniefalco  
> Created_at: 2022-11-27 04:06:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032863476  

we leave it this way for now I have to look closer at it. maybe there's nothing wrong with it.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:10:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194897203  

📁 include/boost/url/experimental/detail/router.hpp

```diff
 199 |+         }
 200 |+ 
 201 |+         // parse nan/inf
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:10:51 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032817367  

wait, WHAT? Why are we processing floating point numbers?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:12:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194897309  

📁 include/boost/url/experimental/detail/router.hpp

```diff
 402 |+         }
 403 |+ 
 404 |+         switch (type_)
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:12:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032817524  

We are only supporting strings and unsigned... all the rest of this has to be removed. I guess you could save it to your personal github account as a souvenir.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-26 18:12:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1194897330  

📁 include/boost/url/experimental/detail/router.hpp

```diff
 493 |+ 
 494 |+     bool
 495 |+     match_chrono(decode_view dseg) const
```

> Username: vinniefalco  
> Created_at: 2022-11-26 18:12:47 UTC  
> Updated_at: 2022-11-26 18:12:48 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032817541  

wtf

> Username: alandefreitas  
> Created_at: 2022-11-26 18:40:38 UTC  
> Updated_at: 2022-11-26 18:41:16 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032820154  

I implemented the fmtlib syntax in https://fmt.dev/latest/syntax.html.  
  
A few types didn't look so useful but leaving it incomplete just to remove a few types seemed confusing to the user.  
  
Surprisingly, the chrono strings did seem useful. For instance in a financial API to get quote at a given time point.

> Username: vinniefalco  
> Created_at: 2022-11-27 04:07:28 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1032863529  

Nope there was no need to duplicate fmtlib and even if there was, we ship with strings/unsigned and then only add additional things on a case by case basis with guidance from stakeholders.


---

## Comment 14

> Username: vinniefalco  
> Created_at: 2022-11-26 18:22:54 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1328092695  

1. we are only supporting strings and unsigned integers  
2. the algorithms need to be non-templates  
  
for doing the non-templating I would suggest this  
```  
class basic_router  
{  
  struct element  
  {  
    node* n; // or whatever  
  };  
  std::size_t sizeof_ = 0; // size of each element  
  std::size_t n_ = 0; // number of elements  
  void* v_; // vector of elements, each sizeof_ in size  
    
  // n == sizeof( T )  
  basic_router( std::size_t n );  
  
public:  
};  
  
template<class T>  
class router : public basic_router;  
```  
  
Put all the implementation of insertion and matching into the base class, and use type-erasure to implement the derived class wrappers which have to return the T.  
  
Each element in the hand-rolled vector will contain the fixed part (struct `element`) followed by the `T`.   
  
When you are done most of the router code will be in `impl/basic_router.ipp` (note ipp file) rather than a hpp file, meaning it is included via src.hpp and not through public headers.

---

## Comment 15

> Username: alandefreitas  
> Created_at: 2022-11-26 18:59:13 UTC  
> Updated_at: 2022-11-30 00:38:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1328097672  

> we are only supporting strings and unsigned integers  
  
Since matching a string is matching anything, isn't it better to just support strings, unless we have a convention to validate these replacements?  
  
My impression is that the point of validating replacements is so that the user doesn't have to "roll back" a match that shouldn't have happened. Or at least avoid false positives as much as possible.  
  
If the user is already canceling these false positives, they can also cancel an invalid unsigned integer match. But if we also support unsigned integers, I foresee dozens of issues by people requesting us to also validate this and that data type until we have a full system with a weird syntax that wasn't planned ahead of time.  
  
There's also a secondary issue with the replacement syntax for that.  
  
> Put all the implementation of insertion and matching into the base class, and use type-erasure to implement the derived class wrappers which have to return the T.  
  
How do we avoid storing T in the nodes? Or will the base class nodes also store some form of `std::any`?

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2022-11-27 04:08:45 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1328167000  

> How do we avoid storing T in the nodes? Or will the base class nodes also store some form of std::any?  
  
You have to store it somehow, obviously. There has to be type-erasing. I can give you the answer if you want. Or you can try to do it yourself as a challenge and then we can compare notes. Either one is fine :) I left big clues in the two class declarations above

---

## Comment 17

> Username: vinniefalco  
> Created_at: 2022-11-27 04:14:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1328167621  

> My impression is that the point of validating replacements is so that the user doesn't have to "roll back" a match that shouldn't have happened. Or at least avoid false positives as much as possible.  
  
I don't know what this business of "rolling back" or "canceling" matches means. It is the responsibility of the user to ensure that there is no ambiguity in routes. We need to talk about this in the channel.

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-11-29 15:50:01 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1330858168  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-11-29 21:37:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331349313  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-11-29 22:17:44 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331392459  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-11-29 22:47:47 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331417254  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-11-29 23:29:55 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331450782  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-29 23:38:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1198499200  

📁 include/boost/url/grammar/impl/range_rule.hpp

```diff
 374 |     iterator(
 375 |-         iterator const&) noexcept = default;
 375 |+         iterator const&) = default;
```

> Username: vinniefalco  
> Created_at: 2022-11-29 23:38:15 UTC  
> Updated_at: 2022-11-29 23:38:16 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035388354  

copies can throw? since when?

> Username: alandefreitas  
> Created_at: 2022-11-29 23:43:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035391133  

That's an error we had with GCC 4.8 and Clang 3.8:  
  
```  
./boost/url/grammar/impl/range_rule.hpp:374:5: error: function 'boost::urls::grammar::range<T>::iterator::iterator(const boost::urls::grammar::range<T>::iterator&) [with T = boost::urls::experimental::detail::segment_template]' defaulted on its first declaration with an exception-specification that differs from the implicit declaration 'boost::urls::grammar::range<boost::urls::experimental::detail::segment_template>::iterator::iterator(const boost::urls::grammar::range<boost::urls::experimental::detail::segment_template>::iterator&)'  
     iterator(  
     ^  
./boost/url/grammar/impl/range_rule.hpp:376:15: error: function 'boost::urls::grammar::range<T>::iterator& boost::urls::grammar::range<T>::iterator::operator=(const boost::urls::grammar::range<T>::iterator&) [with T = boost::urls::experimental::detail::segment_template]' defaulted on its first declaration with an exception-specification that differs from the implicit declaration 'boost::urls::grammar::range<boost::urls::experimental::detail::segment_template>::iterator& boost::urls::grammar::range<boost::urls::experimental::detail::segment_template>::iterator::operator=(const boost::urls::grammar::range<boost::urls::experimental::detail::segment_template>::iterator&)'  
     iterator& operator=(  
               ^  
```  
  
From this answer  
  
https://stackoverflow.com/questions/47032077/noexcept-default-compilation-error  
  
my understanding is the default declarations can throw if the members can throw and the default declaration will no `noexcept` by default whenever this is possible.  
  
But the compiler doesn't include much detail about the members that are causing the error.

> Username: vinniefalco  
> Created_at: 2022-11-30 04:15:26 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035511625  

we need to figure out why it thinks that the members can throw, because I took time to make sure they didn't. I might have forgotten "noexcept" on one of the dependent types.

> Username: vinniefalco  
> Created_at: 2022-11-30 04:16:38 UTC  
> Updated_at: 2022-11-30 04:16:39 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035512075  

it has to be `result<T> rv_;`

> Username: alandefreitas  
> Created_at: 2022-11-30 05:03:19 UTC  
> Updated_at: 2022-11-30 05:03:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035531838  

Yes. Definitely.


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-29 23:39:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1198499829  

📁 include/boost/url/experimental/detail/router.hpp

```diff
 108 |+ // id_start          ::=  "a"..."z" | "A"..."Z" | "_"
 109 |+ // id_continue       ::=  id_start | digit
 110 |+ struct identifier_rule_t
```

> Username: vinniefalco  
> Created_at: 2022-11-29 23:39:19 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035388852  

you shouldn't break it down to this level of granularity... there's no need for an "id rule"


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-29 23:40:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1198500487  

📁 include/boost/url/experimental/detail/router.hpp

```diff
  64 |+ 
  65 |+     string_view
  66 |+     id() const
```

> Username: vinniefalco  
> Created_at: 2022-11-29 23:40:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035389369  

The id is just a comment, what is this function for?

> Username: alandefreitas  
> Created_at: 2022-11-30 05:05:30 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035532846  

This will be used in the match results very soon.  
  
I talked to Klemens about his use cases. He also told me to read the openAPI spec.  
  
I'm convinced the ability to get the value of a named parameters is very important.

> Username: vinniefalco  
> Created_at: 2022-11-30 20:05:17 UTC  
> Updated_at: 2022-11-30 20:05:18 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1036396466  

Klemens' use-case is in formatting not routing

> Username: alandefreitas  
> Created_at: 2022-12-01 00:11:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1036570581  

Yes. That's been considered.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-29 23:40:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1198500845  

📁 include/boost/url/experimental/router.hpp

```diff
  47 |+ */
  48 |+ template <class T>
  49 |+ class router
```

> Username: vinniefalco  
> Created_at: 2022-11-29 23:40:48 UTC  
> Updated_at: 2022-11-29 23:40:49 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035389686  

where's the separation?

> Username: alandefreitas  
> Created_at: 2022-11-30 05:06:01 UTC  
> Updated_at: 2022-11-30 05:06:21 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1035533071  

It's still part of the plan. I was focused on fixing bugs and simplifying the grammar today.


---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-11-30 00:03:52 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331475750  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 28

> Username: codecov[bot]  
> Created_at: 2022-11-30 00:12:52 UTC  
> Updated_at: 2023-01-20 23:18:53 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331482107  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#630](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2068b86) into [develop](https://codecov.io/gh/boostorg/url/commit/9d21228305334d7cbc2422f9b02216179f12c0ec?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9d21228) will **increase** coverage by `0.23%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/630/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #630      +/-   ##  
===========================================  
+ Coverage    96.89%   97.13%   +0.23%       
===========================================  
  Files          146      152       +6       
  Lines         7924     8435     +511       
===========================================  
+ Hits          7678     8193     +515       
+ Misses         246      242       -4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/impl/range\_rule.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3JhbmdlX3J1bGUuaHBw) | `95.49% <ø> (+1.69%)` | :arrow_up: |  
| [example/router/detail/impl/router.ipp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL2ltcGwvcm91dGVyLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [example/router/detail/router.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL3JvdXRlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [example/router/impl/matches.ipp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvaW1wbC9tYXRjaGVzLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [example/router/impl/router.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvaW1wbC9yb3V0ZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [example/router/matches.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvbWF0Y2hlcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [example/router/router.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvcm91dGVyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/decode\_view.ipp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGVfdmlldy5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGVfdmlldy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| ... and [2 more](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9d21228...2068b86](https://codecov.io/gh/boostorg/url/pull/630?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-11-30 04:24:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1331627855  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-12-01 00:19:41 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1332930538  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-01 13:30:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1200991408  

📁 include/boost/url/experimental/detail/router.hpp

```diff
  40 | class segment_template
  41 | {
  42 |+     enum class modifier : std::uint8_t
```

> Username: vinniefalco  
> Created_at: 2022-12-01 13:30:16 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1037109931  

why trot out a clumsy declared type when you could use a built-in type like unsigned char? Please explain the rationale.

> Username: alandefreitas  
> Created_at: 2022-12-01 14:30:37 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1037178417  

I thought it was common practice to use `uint8_t` to express that.

> Username: vinniefalco  
> Created_at: 2022-12-02 06:01:15 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1037811970  

why use a library type when you could use a built-in?


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-01 14:12:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1201060358  

📁 test/unit/experimental/router.cpp

```diff
  84 |+         good("user/{name}/{op?}/b", "user/johndoe/r/b");
  85 |+         good("user/{name}/{op?}/b", "user/johndoe/b");
  86 |+         good("user/{name}/{op?}/{op2?}", "user/johndoe");
```

> Username: vinniefalco  
> Created_at: 2022-12-01 14:12:43 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1037157275  

nice!


---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2022-12-02 00:04:56 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1334598941  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2022-12-29 23:22:46 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1367632957  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2022-12-30 02:24:16 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1367688924  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 36

> Username: vinniefalco  
> Created_at: 2022-12-31 15:03:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1368238534  

I think this should start out as an example rather than some experimental/ thing.

---

## Comment 37

> Username: alandefreitas  
> Created_at: 2023-01-01 16:31:25 UTC  
> Updated_at: 2023-01-01 16:31:48 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1368481916  

We already have a simpler router example. I put a resource tree in there at first but you told me to remove it because it was too much for an example.

---

## Comment 38

> Username: vinniefalco  
> Created_at: 2023-01-01 17:25:41 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1368493137  

I'm allowed to change my mind. Just put it in its own folder in example/ and organize the sources in a way that it is easy to integrate into someone's existing codebase just by copying the files and including them (except for the file with `main`). What do you think?

---

## Comment 39

> Username: alandefreitas  
> Created_at: 2023-01-01 17:27:44 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1368493437  

No problem. I'll move everything to an example. The existing router example is quite different. What name do you suggest for the new example?

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2023-01-03 00:56:54 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369299779  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:00:52 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369301038  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:11:28 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369305628  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:12:18 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369305990  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:22:12 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369309401  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:26:08 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369310566  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:32:11 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369312345  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2023-01-03 01:35:23 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369313354  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2023-01-03 02:12:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369326272  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2023-01-03 02:22:36 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369333375  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2023-01-03 04:06:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369376968  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2023-01-03 04:14:29 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369380322  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2023-01-03 14:12:05 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369813701  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2023-01-03 14:20:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369822885  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2023-01-03 14:57:01 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369862278  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2023-01-03 15:04:35 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1369872335  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2023-01-03 20:21:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1370190057  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2023-01-03 20:30:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1370196783  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2023-01-04 21:17:02 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1371429674  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2023-01-04 21:26:36 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1371441700  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2023-01-04 23:04:12 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1371526496  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2023-01-04 23:13:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1371533258  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 62

> Username: cppalliance-bot  
> Created_at: 2023-01-05 15:37:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372376849  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2023-01-05 15:45:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372387577  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2023-01-05 16:58:42 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372477811  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 65

> Username: cppalliance-bot  
> Created_at: 2023-01-05 17:04:54 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372485839  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 66

> Username: cppalliance-bot  
> Created_at: 2023-01-05 21:47:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372818306  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 67

> Username: cppalliance-bot  
> Created_at: 2023-01-05 21:58:21 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372831062  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 68

> Username: cppalliance-bot  
> Created_at: 2023-01-05 22:21:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372867787  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 69

> Username: cppalliance-bot  
> Created_at: 2023-01-05 22:33:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372877559  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 70

> Username: cppalliance-bot  
> Created_at: 2023-01-05 23:16:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372916283  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 71

> Username: cppalliance-bot  
> Created_at: 2023-01-05 23:22:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1372920393  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 72

> Username: cppalliance-bot  
> Created_at: 2023-01-06 16:07:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373841170  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 73

> Username: cppalliance-bot  
> Created_at: 2023-01-06 16:10:40 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373846461  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 74

> Username: cppalliance-bot  
> Created_at: 2023-01-06 16:36:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373874090  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 75

> Username: cppalliance-bot  
> Created_at: 2023-01-06 16:45:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373881496  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 76

> Username: cppalliance-bot  
> Created_at: 2023-01-06 18:06:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373969081  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 77

> Username: cppalliance-bot  
> Created_at: 2023-01-06 18:09:36 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1373971537  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 78

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:01:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374127154  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 79

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:11:58 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374137441  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 80

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:13:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374138926  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 81

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:24:40 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374150725  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 82

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:27:01 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374152792  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 83

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:33:43 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374159112  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 84

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:35:58 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374161459  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 85

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:42:06 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374167393  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 86

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:46:00 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374171077  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 87

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:49:05 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374173936  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 88

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:55:55 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374180621  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 89

> Username: cppalliance-bot  
> Created_at: 2023-01-06 21:56:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374180727  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 90

> Username: cppalliance-bot  
> Created_at: 2023-01-06 22:21:58 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374202000  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 91

> Username: cppalliance-bot  
> Created_at: 2023-01-06 22:30:02 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1374208078  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 92 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:33:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239488912  

📁 example/router/router.hpp

```diff
 173 |+         @ref resolve.
 174 |+ */
 175 |+ template <class T, std::size_t N = 20>
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:33:37 UTC  
> Updated_at: 2023-01-06 22:33:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063853714  

LOL no.. the idea was to put the N on the `match_results` not the router class. Although, I'm not sure which is better.

> Username: alandefreitas  
> Created_at: 2023-01-06 23:01:34 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063866753  

Good idea. I thought the proposal was to fix it at 20 and that it was being innovative here. :laughing:   
  
What would the API for `router<T>::match` look like in this case?  
  
```  
template <std::size_t N = 20>  
match_results  
match(string_view request) const noexcept;  
```  
  
```  
template <class N = mp_int<20>>  
match_results  
match(string_view request, N) const noexcept;  
```  
  
?

> Username: vinniefalco  
> Created_at: 2023-01-06 23:11:47 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063870374  

ouch... that hurts the eyes...


---

## Review 93 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:35:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239489743  

📁 include/boost/url/grammar/impl/range_rule.hpp

```diff
 374 |     iterator(
 375 |-         iterator const&) noexcept = default;
 375 |+         iterator const&) = default;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:35:16 UTC  
> Updated_at: 2023-01-06 22:35:17 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063854361  

assignment of iterators is supposed to be noexcept, why the change?

> Username: alandefreitas  
> Created_at: 2023-01-06 23:02:59 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063867256  

I removed that `noexcept` a loooooong time ago because I had an error somewhat similar to the error you had in http-proto later. I think some rule ended up not being `noexcept` and things failed but I couldn't find a solution for that.  
  
I'll have another look. Maybe things fixed themselves. I don't know.

> Username: vinniefalco  
> Created_at: 2023-01-06 23:12:24 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063870555  

Oh yes, because of `result<T>` assignment. We need to track that down and reproduce it with a minimal example, and see if Peter can fix it.

> Username: vinniefalco  
> Created_at: 2023-01-10 16:56:09 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1066035102  

we still need to track it down


---

## Review 94 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:38:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239494511  

📁 example/router/router.hpp

```diff
  24 |+ {
  25 |+     class impl;
  26 |+     std::unique_ptr<impl> impl_;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:38:39 UTC  
> Updated_at: 2023-01-06 22:38:40 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063857836  

You never used this... all of that node stuff and those private functions were supposed to be defined in the TU

> Username: alandefreitas  
> Created_at: 2023-01-06 23:04:08 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063867686  

Do you mean "this" the `impl_` member? Everything related to `router_base::impl` is in the `.ipp` file. Where should it be?

> Username: vinniefalco  
> Created_at: 2023-01-06 23:13:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063870952  

Yes `impl_` is unused. It would be better if all those protected and private declarations were in the .ipp file. We can discuss.


---

## Review 95 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:39:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239495161  

📁 example/router/router.hpp

```diff
  91 |+ 
  92 |+         bool
  93 |+         has_error() const;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:39:38 UTC  
> Updated_at: 2023-01-06 22:39:39 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063858309  

The matches can have an error?

> Username: alandefreitas  
> Created_at: 2023-01-06 23:06:31 UTC  
> Updated_at: 2023-01-06 23:06:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063868511  

It tried to model it like a `result` that already provides the container-related functions when they're valid. The point was to avoid a proxy to a proxy and syntaxes like `v = **m` or `v = (*m)[id]`. It ended up always holding the same type of error though (`grammar::mismatch`) but I could include something else.

> Username: vinniefalco  
> Created_at: 2023-01-06 23:13:50 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063871053  

we have to simplify this, by a lot


---

## Review 96 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:41:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239496041  

📁 example/router/router.hpp

```diff
 227 |+     template <class U>
 228 |+     void
 229 |+     route(string_view path, U&& resource);
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:41:12 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063858981  

`resource` wut? no.. just `v` or `t` or `u`. It is not a "resource" it is a user-defined value.


---

## Review 97 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:41:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239496448  

📁 example/router/router.hpp

```diff
 235 |+      */
 236 |+     match_results
 237 |+     match(string_view request) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:41:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063859283  

`request`? No its not a request it is just a string or a path, or something like that. Maybe `pct_string_view` or even `urls::encoded_segments_view`?

> Username: alandefreitas  
> Created_at: 2023-01-06 23:09:03 UTC  
> Updated_at: 2023-01-06 23:09:45 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063869383  

> request? No its not a request it is just a string or a path, or something like that.   
  
Yes. It's more like just a `path` or `target`. `request` would be confusing because it sounds like `http::request` or something more complex.  
  
> Maybe pct_string_view or even urls::encoded_segments_view?  
  
It used to be `pct_string_view` but it ended up not making much sense because we have to reparse it to get the segments. So the first parse was wasted and verbose. At this point, if the request is invalid, we simply don't have a match.

> Username: vinniefalco  
> Created_at: 2023-01-06 23:14:29 UTC  
> Updated_at: 2023-01-06 23:14:30 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063871257  

what about `encoded_segments_view`?

> Username: alandefreitas  
> Created_at: 2023-01-06 23:40:12 UTC  
> Updated_at: 2023-01-06 23:40:13 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063883694  

Yes. That's kind of what it does with the path. I'll check if there are implicit conversions, etc.


---

## Review 98 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:42:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239496572  

📁 example/router/router.hpp

```diff
 246 |+     template <class ...Args>
 247 |+     result<T>
 248 |+     match_to(string_view request, Args&... args) const;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:42:10 UTC  
> Updated_at: 2023-01-06 22:42:11 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063859368  

not a request and why the args

> Username: alandefreitas  
> Created_at: 2023-01-06 23:11:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063870099  

This question was answered in slack, right? (Just want to avoid resolving unresolved comments)

> Username: vinniefalco  
> Created_at: 2023-01-06 23:14:42 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063871343  

Yes it was answered

> Username: alandefreitas  
> Created_at: 2023-01-09 23:48:37 UTC  
> Updated_at: 2023-01-09 23:48:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1065210180  

Removing it


---

## Review 99 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:42:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239496788  

📁 example/router/router.hpp

```diff
 255 |+ } // boost
 256 |+ 
 257 |+ #include <impl/router.hpp>
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:42:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063859505  

Should not be in angle brackets since it is a relative path

> Username: alandefreitas  
> Created_at: 2023-01-06 23:11:31 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063870265  

I put `.` in include paths. I got that from another example but I don't remember which.

> Username: vinniefalco  
> Created_at: 2023-01-06 23:22:55 UTC  
> Updated_at: 2023-01-06 23:22:56 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063874131  

I mean just put it in double quotes


---

## Review 100 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 22:45:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239498139  

📁 example/router/router.cpp

```diff
  37 |+ namespace http = beast::http;
  38 |+ using string_view = urls::string_view;
  39 |+ using match_results = urls::router_base::match_results_base;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 22:45:02 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063860430  

this type is uuuuuuugly

> Username: alandefreitas  
> Created_at: 2023-01-06 23:13:57 UTC  
> Updated_at: 2023-01-06 23:14:41 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063871099  

Yes. We had a kind of problem here.   
  
The ideal type would be `router<T>::match_results` but I can't do that because `T` doesn't exist yet.

> Username: vinniefalco  
> Created_at: 2023-01-07 01:38:36 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063933484  

hmmm I will have to think about that, I believe we can solve it


---

## Review 101 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 23:16:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239513571  

📁 example/router/router.hpp

```diff
 249 |+ 
 250 |+ private:
 251 |+     std::vector<T> data_;
```

> Username: vinniefalco  
> Created_at: 2023-01-06 23:16:48 UTC  
> Updated_at: 2023-01-06 23:16:49 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063872060  

hmmm I wanted this to be in the TU


---

## Review 102 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 23:23:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239516308  

📁 example/router/router_test.cpp

```diff
  12 |+ #include <src.hpp>
  13 |+ 
  14 |+ #include "test_suite.hpp"
```

> Username: vinniefalco  
> Created_at: 2023-01-06 23:23:19 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063874258  

This file should probably go in test/example/router/router.cpp

> Username: alandefreitas  
> Created_at: 2023-01-06 23:38:12 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063883022  

Oh... we can do that!


---

## Review 103 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-06 23:25:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1239517279  

📁 example/router/detail/router.hpp

```diff
 164 |+ // especially the root nodes can potentially an
 165 |+ // exponentially higher number of child nodes.
 166 |+ class child_idx_vector
```

> Username: vinniefalco  
> Created_at: 2023-01-06 23:25:27 UTC  
> Updated_at: 2023-01-06 23:26:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063875018  

Without benchmarks, this is kind of superfluous. I mean, what are you optimizing here, insertions? Those don't need optimization since they only happen once at startup. Memory allocation is essentially free.

> Username: alandefreitas  
> Created_at: 2023-01-06 23:37:54 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1063882918  

Yes. Cache locality more than allocations. But still, yes.


---

## Comment 104

> Username: cppalliance-bot  
> Created_at: 2023-01-09 16:07:15 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375870627  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 105

> Username: cppalliance-bot  
> Created_at: 2023-01-09 16:15:01 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375890631  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 106

> Username: cppalliance-bot  
> Created_at: 2023-01-09 16:22:07 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375900616  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 107

> Username: cppalliance-bot  
> Created_at: 2023-01-09 16:30:56 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375913116  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 108

> Username: cppalliance-bot  
> Created_at: 2023-01-09 17:02:06 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375954002  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 109

> Username: cppalliance-bot  
> Created_at: 2023-01-09 17:05:10 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1375958969  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 110

> Username: cppalliance-bot  
> Created_at: 2023-01-09 20:42:04 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376289077  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 111

> Username: cppalliance-bot  
> Created_at: 2023-01-09 20:50:05 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376296915  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 112

> Username: cppalliance-bot  
> Created_at: 2023-01-10 00:12:01 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376525356  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 113

> Username: cppalliance-bot  
> Created_at: 2023-01-10 00:20:09 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376533733  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 114

> Username: cppalliance-bot  
> Created_at: 2023-01-10 01:42:10 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376605886  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 115

> Username: cppalliance-bot  
> Created_at: 2023-01-10 01:52:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1376612659  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 116 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-10 16:55:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/630#pullrequestreview-1242635906  

📁 example/router/router.hpp

```diff
  80 |+     template <std::size_t N>
  81 |+     T const*
  82 |+     find(segments_encoded_view path, matches& m) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2023-01-10 16:55:24 UTC  
> Url: https://github.com/boostorg/url/pull/630#discussion_r1066034074  

not right...


---

## Comment 117

> Username: cppalliance-bot  
> Created_at: 2023-01-10 21:12:10 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1377863033  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 118

> Username: cppalliance-bot  
> Created_at: 2023-01-10 21:20:38 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1377879305  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 119

> Username: cppalliance-bot  
> Created_at: 2023-01-10 21:27:09 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1377892388  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 120

> Username: cppalliance-bot  
> Created_at: 2023-01-10 21:34:52 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1377908358  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 121

> Username: cppalliance-bot  
> Created_at: 2023-01-10 23:27:02 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378028973  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 122

> Username: cppalliance-bot  
> Created_at: 2023-01-10 23:35:06 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378035199  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 123

> Username: cppalliance-bot  
> Created_at: 2023-01-10 23:46:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378044067  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 124

> Username: cppalliance-bot  
> Created_at: 2023-01-10 23:55:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378051052  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 125

> Username: cppalliance-bot  
> Created_at: 2023-01-11 00:21:57 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378070243  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 126

> Username: cppalliance-bot  
> Created_at: 2023-01-11 00:28:03 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378074749  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 127

> Username: cppalliance-bot  
> Created_at: 2023-01-11 01:07:14 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378104660  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 128

> Username: cppalliance-bot  
> Created_at: 2023-01-11 01:15:24 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1378111210  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 129

> Username: cppalliance-bot  
> Created_at: 2023-01-11 15:52:07 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1379005454  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 130

> Username: cppalliance-bot  
> Created_at: 2023-01-11 15:55:13 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1379013201  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 131

> Username: cppalliance-bot  
> Created_at: 2023-01-12 22:29:02 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1381064910  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 132

> Username: cppalliance-bot  
> Created_at: 2023-01-12 23:13:20 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1381094583  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 133

> Username: cppalliance-bot  
> Created_at: 2023-01-20 21:47:14 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1398990464  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 134

> Username: cppalliance-bot  
> Created_at: 2023-01-20 21:55:33 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1398998862  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 135

> Username: cppalliance-bot  
> Created_at: 2023-01-20 23:07:13 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1399055512  

An automated preview of the documentation is available at [https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://630.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 136

> Username: cppalliance-bot  
> Created_at: 2023-01-20 23:19:17 UTC  
> Url: https://github.com/boostorg/url/pull/630#issuecomment-1399067341  

GCOVR code coverage report [https://630.url.prtest.cppalliance.org/gcovr/index.html](https://630.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://630.url.prtest.cppalliance.org/genhtml/index.html](https://630.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://630.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
