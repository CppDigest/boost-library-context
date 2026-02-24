# #133 refactor: implementation tidy up [Merged]

> Username: typenametea  
> Created at: 2024-09-18 21:15:10 UTC  
> Updated at: 2024-09-19 22:59:48 UTC  
> Merged at: 2024-09-19 22:57:38 UTC  
> Closed at: 2024-09-19 22:57:38 UTC  
> Url: https://github.com/boostorg/optional/pull/133  

C++11 is now the library minimum requirement, so clean up the interface by removing conditional compilation intended to support C++03 alongside C++11.  
  
Removed conditional compilation for;  
- `BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES`  
- `BOOST_NO_CXX11_VARIADIC_TEMPLATES`   
- `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX`  
- `BOOST_NO_CXX11_REF_QUALIFIERS`  
  
I wasn't completely sure if it was okay to remove conditions around variadic templates, unified init syntax, and ref qualifiers - but they are C++11 language features so I assumed it would be okay -  please let me know if not

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2024-09-18 21:25:53 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2359434566  

Thanks!  
The following place defines the presence of which C++11 features you can assume:  
https://github.com/boostorg/optional/blob/develop/include/boost/none_t.hpp#L18  
  
Indeed, the following two are not in the list  
 * `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX`  
 * `BOOST_NO_CXX11_REF_QUALIFIERS`

---

## Comment 2

> Username: typenametea  
> Created_at: 2024-09-18 21:39:40 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2359452776  

Neat, thanks for that link  
  
> Indeed, the following two are not in the list  
>   
>     * `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX`  
>   
>     * `BOOST_NO_CXX11_REF_QUALIFIERS`  
  
Hmm, I do see CXX11_REF_QUALIFIERS in there **_defined(BOOST_NO_CXX11_REF_QUALIFIERS)_** \.  For the unified init syntax I'll put the conditional back in.  
  
Looking at that list there are now more things I notice in the code base that could perhaps be replaced, such as the BOOST_NOEXCEPT macro with just `noexcept`, and defaulting the move functions - should I do that too?

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2024-09-19 03:33:21 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2359906855  

First, some background. The primary goal of now requiring some C++11 features to be present in the user compiler is to reduce dependencies on other Boost libraries: these that serve as workarounds for missing compiler features. A great success was to remove the dependency on Boost.Utility (see [here](https://github.com/boostorg/optional/commit/3a8556186ebc778176863b01060043d6aa1d3cc0)). The goal is not to simplify the implementation of Boost.Optional: this comes as a nice side effect.  
  
Now, to your question. We are in the context of implementing the `constexpr` interface. Getting rid of `BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES`-based conditional definitions will make that task easier. Whereas replacing `BOOST_NOEXCEPT` with `noexcept` will not make the task easier.  
  
Having said that, if you feel motivated to do other modernization as well, then go ahead. My only request is to make any such separate cleanup a separate Pull Request. This will make my reviewer's job easier.

---

## Comment 4

> Username: typenametea  
> Created_at: 2024-09-19 21:23:40 UTC  
> Updated_at: 2024-09-19 21:24:07 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2362225447  

The conditional for `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX` has been added back.  
  
I've kept the changes removing the conditionals for `BOOST_NO_CXX11_VARIADIC_TEMPLATES` and `BOOST_NO_CXX11_REF_QUALIFIERS` within this PR, reason being that I believe it simplifies the interface within the context of implementing the constexpr interface as it will reduce the number of overloads. For example, those conditionals are often used in conjunction with `BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES`, such as;  
  
`#if (!defined BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES) && (!defined BOOST_NO_CXX11_VARIADIC_TEMPLATES)`  
or  
`#if (!defined BOOST_NO_CXX11_REF_QUALIFIERS) && (!defined BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES)`  
  
It makes sense to me to also remove these now, however if you disagree with this I can split those into another pull request

---

## Review 5 [Commented]

> Username: akrzemi1  
> Created_at: 2024-09-19 22:35:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/133#pullrequestreview-2316858673  

📁 include/boost/optional/optional.hpp

```diff
 840 | 
 841 |- 
 841 |+ #ifndef BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX
```

> Username: akrzemi1  
> Created_at: 2024-09-19 22:35:52 UTC  
> Updated_at: 2024-09-19 22:35:53 UTC  
> Url: https://github.com/boostorg/optional/pull/133#discussion_r1767672097  

This is suspicious. The code that is guarded by the macro does not use the uniform initialization at all.   
Maybe the wrong macro was used originally.

> Username: akrzemi1  
> Created_at: 2024-09-19 22:37:10 UTC  
> Url: https://github.com/boostorg/optional/pull/133#discussion_r1767672738  

Anyway, maybe lets keep it.


---

## Comment 6

> Username: akrzemi1  
> Created_at: 2024-09-19 22:37:58 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2362320393  

Do unit tests all pass?

---

## Comment 7

> Username: typenametea  
> Created_at: 2024-09-19 22:59:47 UTC  
> Url: https://github.com/boostorg/optional/pull/133#issuecomment-2362342819  

Indeed the tests pass, I checked with C++11.

---
