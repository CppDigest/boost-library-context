# #13 Add erase_if (C++20) [Merged]

> Username: m-peko  
> Created at: 2020-11-25 14:28:56 UTC  
> Updated at: 2021-01-18 20:59:21 UTC  
> Merged at: 2021-01-18 20:59:21 UTC  
> Closed at: 2021-01-18 20:59:21 UTC  
> Url: https://github.com/boostorg/static_string/pull/13  

Implementation and tests for #10   
  
Please, let me know if something needs to be added or fixed.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-11-25 17:30:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-538718864  

📁 .gitignore

```diff
   1 | bin/
   2 | bin64/
   3 |+ build/
```

> Username: vinniefalco  
> Created_at: 2020-11-25 17:30:39 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r530540061  

This has to go in your personal .gitignore, because "build/" is a well-defined Boost directory. For example: https://github.com/boostorg/json/tree/develop/build  
  
This commit needs to be removed from the pull request.

> Username: m-peko  
> Created_at: 2020-11-26 07:15:17 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r530814115  

Done


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-11-25 17:30:56 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-733847619  

The commit "Add build directory to .gitignore" should not be merged.

---

## Comment 3

> Username: m-peko  
> Created_at: 2020-11-25 17:58:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-733861967  

Good point! I will remove this commit. Anything else to be changed?

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-11-29 08:11:23 UTC  
> Updated_at: 2020-12-18 06:01:22 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-735359075  

# [Codecov](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=h1) Report  
> Merging [#13](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=desc) (4a4345e) into [develop](https://codecov.io/gh/boostorg/static_string/commit/c59150c0423362360eed6ebc1f9282ca6a8a868e?el=desc) (c59150c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/static_string/pull/13/graphs/tree.svg?width=650&height=150&src=pr&token=LSsgELMWac)](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           develop       #13   +/-   ##  
=========================================  
  Coverage   100.00%   100.00%             
=========================================  
  Files            1         1             
  Lines          862       871    +9       
=========================================  
+ Hits           862       871    +9       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/static\_string/static\_string.hpp](https://codecov.io/gh/boostorg/static_string/pull/13/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9zdGF0aWNfc3RyaW5nL3N0YXRpY19zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=footer). Last update [c59150c...798a37e](https://codecov.io/gh/boostorg/static_string/pull/13?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-11-29 22:15:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-540527995  

📁 include/boost/static_string/static_string.hpp

```diff
2703 |+     typename ForwardIt,
2704 |+     typename UnaryPredicate,
2705 |+     typename std::enable_if<detail::is_forward_iterator<ForwardIt>::value>::type* = nullptr>
```

> Username: vinniefalco  
> Created_at: 2020-11-29 22:15:36 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r532276685  

this needs to be ifdeffed for the docs to excclude it, and the javadoc needs a "@par Constraints" section.

> Username: m-peko  
> Created_at: 2020-11-30 06:58:14 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r532380930  

Done


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-11-29 22:15:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-540528008  

📁 include/boost/static_string/static_string.hpp

```diff
6194 |+   typename ForwardIt,
6195 |+   typename UnaryPredicate,
6196 |+   typename std::enable_if<detail::is_forward_iterator<ForwardIt>::value>::type*>
```

> Username: vinniefalco  
> Created_at: 2020-11-29 22:15:45 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r532276707  

same here

> Username: m-peko  
> Created_at: 2020-11-30 06:58:22 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r532380957  

Done


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-12-01 20:20:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-542264387  

📁 include/boost/static_string/static_string.hpp

```diff
2647 |+       @par Constraints
2648 |+ 
2649 |+       `UnaryPredicate` satisfies __UnaryPredicate__.
```

> Username: vinniefalco  
> Created_at: 2020-12-01 20:20:57 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r533696653  

is __UnaryPredicate__ defined in the qbk? It would go here:  
https://github.com/boostorg/static_string/blob/develop/doc/qbk/main.qbk#L34


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-12-01 20:21:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-542264513  

📁 include/boost/static_string/static_string.hpp

```diff
6206 |+   typename UnaryPredicate
6207 |+ #ifndef BOOST_STATIC_STRING_DOCS
6208 |+     , typename std::enable_if<detail::is_forward_iterator<ForwardIterator>::value>::type*
```

> Username: vinniefalco  
> Created_at: 2020-12-01 20:21:07 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r533696751  

Good


---

## Comment 9

> Username: sdkrystian  
> Created_at: 2020-12-01 20:53:38 UTC  
> Updated_at: 2020-12-01 20:54:24 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-736813217  

@m-peko It seems that you implemented `erase_if` as a member function -- and while your implementation is good, this differs from the standard in that `std::erase_if(std::basic_string&, UnaryPredicate)` is a *non-member* function (and only has a single overload). So to match it, you would ideally want to implement a function with the following signature:  
```cpp  
template<std::size_t N, typename CharT,   
    typename Traits, typename UnaryPredicate>  
BOOST_STATIC_STRING_CPP14_CONSTEXPR  
typename // required for C++11  
basic_static_string<N, CharT, Traits>::size_type  
erase_if(  
    basic_static_string<N, CharT, Traits>& str,  
    UnaryPredicate pred);  
```  
@vinniefalco what are your thoughts?

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-12-01 21:06:29 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-736820391  

`erase_if` is a non member function?

---

## Comment 11

> Username: sdkrystian  
> Created_at: 2020-12-01 21:19:10 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-736826963  

Yup, see https://en.cppreference.com/w/cpp/string/basic_string/erase2

---

## Comment 12

> Username: m-peko  
> Created_at: 2020-12-01 21:43:20 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-736839196  

@sdkrystian ok sorry for that. I will update the PR with this change.

---

## Comment 13

> Username: m-peko  
> Created_at: 2020-12-02 09:03:01 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-737092611  

I made `erase_if` a non-member function now and reduced the number of tests because there is not that many overloads as before.  
I removed javadocs too because I saw `swap` function does not have javadocs either so I suppose the convention is to have javadocs only for member functions, right?  
Anyway, let me know if something more needs to be updated.

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2020-12-02 09:08:08 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-737095393  

An automated preview of the documentation is available at [https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html](https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html)

---

## Comment 15

> Username: m-peko  
> Created_at: 2020-12-17 10:30:01 UTC  
> Updated_at: 2020-12-17 10:30:29 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747353530  

@sdkrystian @vinniefalco  Any further comments to this PR?

---

## Comment 16

> Username: sdkrystian  
> Created_at: 2020-12-17 15:09:39 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747496334  

Apologies, finals have kept me busy :) I'll give it another look over today and we'll be good to merge

---

## Review 17 [Commented]

> Username: sdkrystian  
> Created_at: 2020-12-17 16:48:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/13#pullrequestreview-554788849  

📁 include/boost/static_string/static_string.hpp

```diff
5625 |+     typename Traits, typename UnaryPredicate>
5626 |+ BOOST_STATIC_STRING_CPP14_CONSTEXPR
5627 |+ typename // required for C++11
```

> Username: sdkrystian  
> Created_at: 2020-12-17 16:46:08 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r545238568  

We don't quite need this comment, but this is just a nitpick.

> Username: m-peko  
> Created_at: 2020-12-17 17:01:41 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r545249977  

Done

---

📁 include/boost/static_string/static_string.hpp

```diff
5637 |+       *first++ = std::move(*it);
5638 |+     else
5639 |+       ++count;
```

> Username: sdkrystian  
> Created_at: 2020-12-17 16:48:05 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r545240071  

Instead of maintaining `count` we could simply return `str.end() - first`

> Username: m-peko  
> Created_at: 2020-12-17 17:01:37 UTC  
> Updated_at: 2020-12-18 06:01:18 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#discussion_r545249913  

Good point! Done


---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2020-12-17 17:08:10 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747573027  

An automated preview of the documentation is available at [https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html](https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html)

---

## Comment 19

> Username: sdkrystian  
> Created_at: 2020-12-17 17:13:15 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747575913  

One more thing -- could you open a separate PR for the whitespace changes please?

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2020-12-17 17:18:09 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747578701  

An automated preview of the documentation is available at [https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html](https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html)

---

## Comment 21

> Username: m-peko  
> Created_at: 2020-12-17 18:04:56 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747604853  

> One more thing -- could you open a separate PR for the whitespace changes please?  
  
Sure, but tomorrow morning. I am away from keyboard now.

---

## Comment 22

> Username: sdkrystian  
> Created_at: 2020-12-17 18:06:19 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747605547  

Appreciate it and no worries, take your time :)

---

## Comment 23

> Username: m-peko  
> Created_at: 2020-12-18 06:03:56 UTC  
> Updated_at: 2020-12-18 06:05:10 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747888417  

@sdkrystian done. Btw, if there would be any other issue in this repo, please feel free to assign it to me if help is needed :)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2020-12-18 06:08:09 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-747889846  

An automated preview of the documentation is available at [https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html](https://13.static-string.prtest.cppalliance.org/libs/static-string/doc/html/index.html)

---

## Comment 25

> Username: sdkrystian  
> Created_at: 2020-12-29 08:45:38 UTC  
> Url: https://github.com/boostorg/static_string/pull/13#issuecomment-751998413  

Awesome. Sorry for the delay, I'll be merging this tomorrow.

---
