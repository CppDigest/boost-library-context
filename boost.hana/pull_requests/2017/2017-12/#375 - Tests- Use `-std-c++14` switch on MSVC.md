# #375 Tests: Use `/std:c++14` switch on MSVC [Closed]

> Username: Kojoley  
> Created at: 2017-12-25 17:47:23 UTC  
> Updated at: 2018-01-01 22:12:31 UTC  
> Closed at: 2018-01-01 21:25:10 UTC  
> Url: https://github.com/boostorg/hana/pull/375  



---

## Comment 1

> Username: ldionne  
> Created_at: 2018-01-01 20:05:01 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354673251  

We don't have tests on MSVC right now since the compiler is not supported; have you managed to build the tests using some version of MSVC?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-01-01 20:29:29 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354674597  

I used to add a pre-check for hana tests because [currently regression matrix](http://www.boost.org/development/tests/develop/developer/hana.html) looks terrible with all the compilation failures on unsupported compilers, but even latest MSVC does not pass `cxx14_constexpr`, so if add them globally all the tests will be disabled. I just wanted to enable c++14 on MSVC to recognize if it makes the things better and maybe any subset of hana could be used on MSVC and pre-checks should be per test but not for the whole project.  
  
I can open a PR with a global pre-check if you are interested.

---

## Comment 3

> Username: ldionne  
> Created_at: 2018-01-01 20:36:14 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354674900  

When you talk about a "pre-check", do you mean an expected failure to the Boost regression matrix?  
  
In any case, I'm fine with the PR because the flag is probably needed when _trying_ to build on MSVC, as you mention.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-01-01 20:41:32 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354675136  

> When you talk about a "pre-check", do you mean an expected failure to the Boost regression matrix?  
  
No, I mean this https://github.com/Kojoley/hana/commit/ec6ef060011cdff6d3a810ba866dbb0a11c31551.

---

## Comment 5

> Username: ldionne  
> Created_at: 2018-01-01 20:43:29 UTC  
> Updated_at: 2018-01-01 20:44:12 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354675245  

Ahh, excuse my Bjam illiteracy. Yes, I would welcome a PR adding such a pre-check.  
  
Is this PR still necessary with a pre-check?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-01-01 20:51:20 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354675630  

> Ahh, excuse my Bjam illiteracy.  
  
B2 is a mad thing however `requires` actually not a b2 feature but Boost.Config.  
  
> Yes, I would welcome a PR adding such a pre-check.  
  
The question: should it be global?  Because `cxx14_constexpr` will no pass on MSVC while some of hana test are green on it.

---

## Comment 7

> Username: ldionne  
> Created_at: 2018-01-01 20:54:09 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354675749  

I believe it should be global, a bit like the C++14 requirement is global in CMake.  
  
It would indeed be more granular to set it for each test, but this is too much work IMO. Also, whether a test needs feature X or Y can depend on how the part of Hana that it uses are implemented. I'd like to avoid being in a situation where I change how something is implemented and suddenly the pre-checks for some specific unit test become invalid. So, unless what I'm saying does not make sense (which is quite possible given my fragile understanding of pre-checks), I'd say let's go with global.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-01-01 21:21:52 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354678050  

The `/std:c++14` flag on MSVC 14.1 (VS 15.5.2) fixes only a single test: `basic_tuple~auto~sequence`. LMAO.  
As expected, `/std:c++17` does not make it better than `/std:c++14`.

---

## Comment 9

> Username: ldionne  
> Created_at: 2018-01-01 22:12:31 UTC  
> Url: https://github.com/boostorg/hana/pull/375#issuecomment-354680890  

Superseded by #378.

---
