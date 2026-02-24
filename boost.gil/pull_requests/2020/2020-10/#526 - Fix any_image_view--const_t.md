# #526 Fix any_image_view::const_t [Merged]

> Username: sdebionne  
> Created at: 2020-10-21 17:06:10 UTC  
> Updated at: 2022-05-20 09:13:04 UTC  
> Merged at: 2021-03-18 09:53:54 UTC  
> Closed at: 2021-03-18 09:53:54 UTC  
> Url: https://github.com/boostorg/gil/pull/526  

### Description  
  
Fix `any_image_view<>::const_t` that currently returns an invalid type, see the provided test case that fails on `develop`.  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-10-21 17:11:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/526#pullrequestreview-513982740  

An interesting catch! LGTM, just a few nitpicks

📁 test/extension/dynamic_image/any_image_view.cpp

```diff
   6 |+ // http://www.boost.org/LICENSE_1_0.txt
   7 |+ //
   8 |+ #include <type_traits>
```

> Username: mloskot  
> Created_at: 2020-10-21 17:08:51 UTC  
> Updated_at: 2021-03-15 11:46:39 UTC  
> Url: https://github.com/boostorg/gil/pull/526#discussion_r509456946  

Nitpick: I'd move this below `#include <boost/core/lightweight_test_trait.hpp>` as per suggested/consistent https://github.com/boostorg/gil/wiki/Include-Directives-Order

> Username: sdebionne  
> Created_at: 2020-10-22 09:55:50 UTC  
> Updated_at: 2021-03-15 11:46:39 UTC  
> Url: https://github.com/boostorg/gil/pull/526#discussion_r510033506  

Oupsy, sorry I usually include the other way around, from std then 3rd parties to mylib. I'll fix that.

---

📁 test/extension/dynamic_image/any_image_view.cpp

```diff
  15 |+ 
  16 |+ #include "test_fixture.hpp"
  17 |+ #include "core/image/test_fixture.hpp"
```

> Username: mloskot  
> Created_at: 2020-10-21 17:10:57 UTC  
> Updated_at: 2021-03-15 11:46:39 UTC  
> Url: https://github.com/boostorg/gil/pull/526#discussion_r509458562  

I think these can be removed as unused.

---

📁 test/extension/dynamic_image/any_image_view.cpp

```diff
  18 |+ 
  19 |+ namespace gil = boost::gil;
  20 |+ namespace fixture = boost::gil::test::fixture;
```

> Username: mloskot  
> Created_at: 2020-10-21 17:11:12 UTC  
> Updated_at: 2021-03-15 11:46:39 UTC  
> Url: https://github.com/boostorg/gil/pull/526#discussion_r509458793  

and this can be removed as unused too


---

## Comment 2

> Username: sdebionne  
> Created_at: 2020-10-23 07:54:18 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715111112  

I found an other issue similar to #486 (aka `any_image_view<...> v1 = v0` fails to compile) and added the tests accordingly.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-10-23 08:03:38 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715127604  

I thought using inheriting constructors was the right think to do, but it looks like it fails with GCC5, is it C++11 feature complete?

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-10-23 08:19:45 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715156488  

https://gcc.gnu.org/projects/cxx-status.html#cxx11 says  
  
> GCC 4.8.1 was the first feature-complete implementation of the 2011 C++ standard  
  
However, we have dropped support from GCC 4.8 as its C++11 support is unusable for GIL (and Boost.MP11 too), see https://github.com/boostorg/gil/pull/296  
  
Perhaps GCC 5 is lacking or suffering a bug.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2020-10-23 09:15:43 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715214555  

Do you want to support GCC5, in which case I can revert to declaring every constructors (including the missing one to support initializing construction)?

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-10-23 09:26:08 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715220681  

> Do you want to support GCC5  
  
Good question.  
  
Dropping support for GCC 5 could be part of switching to C++17 which was seeded on the Slack, https://cpplang.slack.com/archives/CSVT0STV2/p1588870474195500  
I'd not object it, but we need to collect opinions from others (may be worth posting to Boost or Boost GIL mailnig list)  
/cc @chhenning @lpranam @stefanseefeld

---

## Comment 7

> Username: sdebionne  
> Created_at: 2020-10-23 10:06:38 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-715242849  

Not sure if this is the one, but there are bugs related to inheriting constructors in GCC 5:  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=62310

---

## Comment 8

> Username: mloskot  
> Created_at: 2020-10-26 20:07:01 UTC  
> Updated_at: 2020-10-27 15:58:03 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-716793012  

@sdebionne Any workaround to keep compatible w/ GCC 5 is best option for the time being.  
  
UPDATE: See also @pdimov 's suggestion here https://lists.boost.org/Archives/boost/2020/10/250222.php

---

## Comment 9

> Username: sdebionne  
> Created_at: 2020-10-28 15:23:10 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-718007963  

I am OK with both approaches, aka workaround or dropping GCC 5, but the later and making C++ 14 the new language std requirement is more appealing to me.

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-10-29 09:14:26 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-718520245  

@sdebionne Yes, let's prepare for the step towards C++14, https://lists.boost.org/boost-gil/2020/10/0469.php

---

## Comment 11

> Username: sdebionne  
> Created_at: 2020-11-06 09:07:29 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-722965738  

@mloskot To clarify for 1.75, are we dropping GCC 5 (in which case the checks should pass and this PR could be merged) or do you prefer a workaround? Or are we too late anyway?

---

## Comment 12

> Username: pdimov  
> Created_at: 2020-11-06 16:34:19 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-723175446  

1.75 has just been frozen for beta.

---

## Comment 13

> Username: mloskot  
> Created_at: 2020-11-08 17:43:02 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-723642610  

@sdebionne   
> To clarify for 1.75, are we dropping GCC 5  
  
No, I don't think it's right to drop GCC 5 or start requiring C++14 from 1.75.  
My plan is to add notice to Boost 1.75 release notes that GCC 5 support will be dropped in next release and C++14 will likely be required version too.

---

## Comment 14

> Username: mloskot  
> Created_at: 2020-11-10 08:18:19 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-724540995  

I've announced our plans in the release notes, 8b1c2d3ea4eb5bf0a1e7e093862962313dd6c2bb and https://github.com/boostorg/website/pull/562

---

## Comment 15

> Username: sdebionne  
> Created_at: 2021-03-02 13:56:01 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-788926525  

If I am correct, I need to close and reopen the PR to get the updated pipeline without GCC5? Or is there a better way?

---

## Comment 16

> Username: mloskot  
> Created_at: 2021-03-02 15:17:12 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-788983676  

@sdebionne I think you need to rebase your `sdebionne:fix-any-image-view-const-t` branch and `git push --force` it to update this PR, then it will rebuild.

---

## Comment 17

> Username: sdebionne  
> Created_at: 2021-03-02 16:37:41 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-789041682  

Done. But I still see gcc-5 on Azure and gcc-4.9 on Github Actions, both versions probably impacted by the bug mentioned before.

---

## Comment 18

> Username: mloskot  
> Created_at: 2021-03-02 17:01:42 UTC  
> Updated_at: 2021-03-02 17:02:05 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-789058995  

@sdebionne Sorry, there had been no update to our CI. Fixing in https://github.com/boostorg/gil/pull/572

---

## Comment 19

> Username: mloskot  
> Created_at: 2021-03-02 18:26:10 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-789116059  

@sdebionne The #572 has been merged. If you could update this PR with the develop, then it should build fine.

---

## Comment 20

> Username: sdebionne  
> Created_at: 2021-03-03 10:16:16 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-789601913  

How should I interpret the result of the CI:  
 - Azure (ubuntu1604_gcc6_cxx14_cmake) fails  
 - GA (gcc-6, 11,14,1z, ubuntu-16.04, g++-6) passes

---

## Comment 21

> Username: mloskot  
> Created_at: 2021-03-03 12:15:56 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-789672917  

That is a puzzle! I can't get why the `subimage_view` fails there. I'll have a look later tonight.

---

## Review 22 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-03 21:17:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/526#pullrequestreview-603359100  

@sdebionne I was able to reproduce the build failure locally. The reason why the GitHub Actions does not reveal it is because the new `any_image_view.cpp` is missing from here:  
  
https://github.com/boostorg/gil/blob/3e729e5dae4c0ba6f407f6885bf6a18d525e8489/test/extension/dynamic_image/Jamfile#L13-L14  
  
The GitHub Actions run the tests using B2 (i.e. `Jamfile`-s configuration), while the Azure Pipelines has also the CMake-based jobs.

---

## Comment 23

> Username: sdebionne  
> Created_at: 2021-03-04 08:10:26 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-790414937  

@mloskot Thanks a lot for looking into this! I forgot that we have two build systems... I'll update the Jamfile.  
  
I am a bit puzzled with the error reported by gcc-6, I'll see if I can find a workaround since I think we need to support that version.

---

## Comment 24

> Username: mloskot  
> Created_at: 2021-03-04 08:39:44 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-790432759  

@sdebionne  
> I forgot that we have two build systems  
  
Yes, it is a bit PITA to maintain the two.  
The `Jamfile`-s for B2 are the 'official' build configuration since it is required for integration with Boost.  
The CMake configuration is what we maintain for our own (and contributors) convenience.  
  
My plan is two-fold:  
1. Replace our custom `CMakeLists.txt`-s with the semi-official CMake for Boost https://github.com/boostorg/cmake, or at least integrate GIL with that new infrastructure.  
2. Further simplification of our CI: ideally, if we have single one to maintain, that is, GitHub Actions only, but it needs to cover a) all tests (core and extensions, including IO); b) all compilers we want; c) B2 and CMake   
  
> I'll see if I can find a workaround since I think we need to support that version.  
  
Well, it would simplify things if we can support GCC 6,  
Alternative, is to bump the C++ requirement to C++17, instead of C++14 as we have it now.  
I, personally, am usually more eager to move from C++11 to C++17 than from C++11 to C++14.   
So, I'd not mind, especially that next GIL release will happen around Boost 1.77 or 1.78, so plenty of time.  
/cc @lpranam @simmplecoder

---

## Comment 25

> Username: codecov[bot]  
> Created_at: 2021-03-04 09:12:18 UTC  
> Updated_at: 2021-03-15 13:28:20 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-790457903  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/526?src=pr&el=h1) Report  
> Merging [#526](https://codecov.io/gh/boostorg/gil/pull/526?src=pr&el=desc) (7a4f7d9) into [develop](https://codecov.io/gh/boostorg/gil/commit/8bd2413127237e6fdb3a903ea662bd5e0dfc1b12?el=desc) (8bd2413) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #526      +/-   ##  
===========================================  
- Coverage    78.59%   78.59%   -0.01%       
===========================================  
  Files          117      117                
  Lines         4980     5003      +23       
===========================================  
+ Hits          3914     3932      +18       
- Misses        1066     1071       +5       
```

---

## Comment 26

> Username: sdebionne  
> Created_at: 2021-03-04 10:54:40 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-790525288  

Hi @pdimov, I am encountering an issue which I think is related to inheriting constructors from Variant2 `variant` and older compilers (clang < 4 and gcc < 7). Any chance that this rings a bell?  
  
The pattern is as follow:  
  
```c++  
template <typename ...Views>  
class any_image_view : public variant2::variant<Views...>  
{  
    using parent_t = variant2::variant<Views...>;  
  
public:      
    using parent_t::parent_t;  
    ...  
}  
```  
  
Here is [the error message](https://github.com/boostorg/gil/pull/526/checks?check_run_id=2029240672#step:6:1462) simplified for readability:  
  
```  
./boost/gil/image_view.hpp:100:81: error: no member named 'pixels' in 'boost::gil::any_image_view<...>'  
    image_view(View const& view) : _dimensions(view.dimensions()), _pixels(view.pixels()) {}  
                                                                           ~~~~ ^  
./boost/variant2/variant.hpp:640:90: note: in instantiation of function template specialization 'boost::gil::image_view<...>::image_view<boost::gil::any_image_view<...>' requested here  
    template<class... A> constexpr variant_storage_impl( mp11::mp_size_t<0>, A&&... a ): first_( std::forward<A>(a)... )  
                                                                                         ^  
./boost/mp11/algorithm.hpp:1039:28: note: in instantiation of function template specialization 'test_any_image_view_init_ctor::operator()<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> > >' requested here  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
```

---

## Comment 27

> Username: sdebionne  
> Created_at: 2021-03-08 12:57:56 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-792738096  

I guess I need to extract a separate test case on Godbolt to get some help...

---

## Comment 28

> Username: sdebionne  
> Created_at: 2021-03-12 09:45:22 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-797371025  

@pdimov Here is a godbolt that, I think, shows our problem with (clang < 4 and gcc < 7 and probably MSVC).   
  
https://godbolt.org/z/fs44cz  
  
I am not saying that it's related to Boost.Variant2 but would really appreciate your insight.

---

## Comment 29

> Username: pdimov  
> Created_at: 2021-03-12 15:05:09 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-797547592  

It's indeed related to Variant2; please open an issue there. I don't know yet whether it can be fixed, but I'll give it a try. The problem is that the variant constructor performs overload resolution over all alternatives and this instantiates the greedy `foo(T const&)` converting constructor.

---

## Comment 30

> Username: sdebionne  
> Created_at: 2021-03-15 14:49:18 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-799480557  

@mloskot I am glad that this issue is now fixed. Maybe deprecating the old compilers was not necessary after all? Side question: isn't the new codecov check a bit picky? If we want to save polar :bear:, we might not want to recompile for 10 platforms/compilers for a -0.01% regression in code coverage...

---

## Comment 31

> Username: mloskot  
> Created_at: 2021-03-16 08:59:07 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-800079440  

> Maybe deprecating the old compilers was not necessary after all?  
  
You mean GCC 5 and switch to C++14?  
Well, let's agree that we're slowly heading towards the convenience of C++17 :-)  
  
>  Side question: isn't the new codecov check a bit picky?  We might not want to recompile for 10 platforms/compilers for a -0.01% regression in code coverage...  
  
Hmm, I'm not sure what you mean, since  
  
https://github.com/boostorg/gil/blob/a82af6d25de304bd7482fdfece3b6f775f2832b9/.github/workflows/coverage.yml#L41  
  
but we definitely don't want to generate coverage building with N compilers, just one should be enough.  
  
@avinal Perhaps you, as contributor of the Codecov GA, could comment on that?

---

## Comment 32

> Username: avinal  
> Created_at: 2021-03-16 09:17:21 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-800091586  

> @avinal Perhaps you, as a contributor of the Codecov GA, could comment on that?  
  
Hello @mloskot and @sdebionne, I don't think code coverage is building on more than one platform. There is a single configuration and only one build job.   
  
https://github.com/boostorg/gil/blob/a82af6d25de304bd7482fdfece3b6f775f2832b9/.github/workflows/coverage.yml#L46-L49  
For further clarification, you can compare it with CI config, there are N configs in the matrix and it runs for every single of them.  
https://github.com/boostorg/gil/blob/6da59cc3351e5657275d3a536e0b6e7a1b6ac738/.github/workflows/ci.yml#L127-L131  
  
  
Also, CI and Code Coverage jobs are in a different workflow. If you don't want to recompile for all, you can force stop other jobs, in that case, only one compilation job will run, and only that job is needed for code coverage.  
  
Now when I see the config, I think, this line is kinda unnecessary, we can remove it if you think it is of no use in the compilation  
https://github.com/boostorg/gil/blob/a82af6d25de304bd7482fdfece3b6f775f2832b9/.github/workflows/coverage.yml#L41

---

## Comment 33

> Username: sdebionne  
> Created_at: 2021-03-16 13:56:49 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-800278678  

> Well, let's agree that we're slowly heading towards the convenience of C++17 :-)  
  
Absolutely!  
  
> > Side question: isn't the new codecov check a bit picky?  We might not want to recompile for 10 platforms/compilers for a -0.01% regression in code coverage...  
>   
> Hmm, I'm not sure what you mean, since  
  
Sorry I wasn't clear, I meant that this PR fails for a -0.01% in code coverage. I could fix this eventually, but then we would need to rebuild for n platforms. Or is Codecov just an indicator that should not block a PR to be merged?

---

## Comment 34

> Username: mloskot  
> Created_at: 2021-03-16 16:03:20 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-800392058  

@sdebionne   
> Or is Codecov just an indicator that should not block a PR to be merged?  
  
A very good point!  
Yes, it should be considered as an indicator only with informational purpose and it should not block the merge.   
  
@avinal Thank you for the explanation. By the way, do you know if we can configure the coverage threshold e.g. set it to `1-2 %`? Alternatively, we could allow the Codecov built pass regardless of the result.

---

## Review 35 [Approved]

> Username: mloskot  
> Created_at: 2021-03-16 16:04:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/526#pullrequestreview-613438571  

LGTM. Shall we merge?

---

## Comment 36

> Username: avinal  
> Created_at: 2021-03-16 16:07:48 UTC  
> Updated_at: 2021-03-16 16:11:05 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-800395514  

Welcome @mloskot, I think this https://docs.codecov.io/docs/codecovyml-reference#coverageprecision is what you are talking about, not sure though.  
Setting it to `1` will reduce the precision to one decimal place

---

## Comment 37

> Username: sdebionne  
> Created_at: 2021-03-18 09:09:05 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-801756856  

> LGTM. Shall we merge?  
  
If you are asking me, I would say yes! Or do I need to rebase?

---

## Comment 38

> Username: mloskot  
> Created_at: 2021-03-18 09:54:01 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-801784846  

Thanks

---

## Comment 39

> Username: mloskot  
> Created_at: 2022-05-20 09:13:04 UTC  
> Url: https://github.com/boostorg/gil/pull/526#issuecomment-1132669091  

@sdebionne   
> I am OK with both approaches, aka workaround or dropping GCC 5, but the later and making C++ 14 the new language std requirement is more appealing to me.  
  
Let's do it for Boost 1.80. I have moved the planning towards C++14/17 to discussion here https://github.com/boostorg/gil/discussions/676

---
