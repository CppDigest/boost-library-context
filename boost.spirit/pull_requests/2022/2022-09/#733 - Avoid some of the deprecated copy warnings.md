# #733 Avoid some of the deprecated copy warnings [Closed]

> Username: apolukhin  
> Created at: 2022-09-08 11:42:36 UTC  
> Updated at: 2022-09-17 16:08:49 UTC  
> Closed at: 2022-09-13 01:13:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/733  

The patch fixes only some of the warnings. Remaining warnings could be located via `./b2 libs/spirit/test/ cxxstd=17 "cxxflags=-Werror=deprecated-copy" toolset=clang-15 -j4`

---

## Comment 1

> Username: Kojoley  
> Created_at: 2022-09-08 18:25:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/733#issuecomment-1241075600  

Thanks, I was fixing these earlier this year but got sidetracked by fixing it in the dependencies first, there still warnings coming from Fusion and I have a patch for it I need to file upstream. I don't like that we change type triviality to suppress a useless warning, so I had been replacing fake-assign operators with just warning suppression.

---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2022-09-08 18:26:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/733#pullrequestreview-1101217119  

📁 include/boost/spirit/home/support/unused.hpp

```diff
  29 |         BOOST_DEFAULTED_FUNCTION(unused_type(), {})
  30 |+         BOOST_DEFAULTED_FUNCTION(unused_type(const unused_type& ), {})
  31 |+         BOOST_DEFAULTED_FUNCTION(unused_type& operator=(const unused_type& ), { return *this; })
```

> Username: Kojoley  
> Created_at: 2022-09-08 18:26:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/733#discussion_r966288724  

I don't think these are needed...

> Username: apolukhin  
> Created_at: 2022-09-09 09:12:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/733#discussion_r966815213  

Reverted


---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-09-12 17:39:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/733#issuecomment-1244079414  

@apolukhin I'm waiting on https://github.com/boostorg/phoenix/pull/108 to be merged.

---
