# #86 Refactor single-program channel test to Boost.Test suites and cases [Merged]

> Username: mloskot  
> Created at: 2018-04-27 22:52:50 UTC  
> Updated at: 2018-04-28 18:31:07 UTC  
> Merged at: 2018-04-28 13:48:23 UTC  
> Closed at: 2018-04-28 13:48:23 UTC  
> Url: https://github.com/boostorg/gil/pull/86  

### Description  
  
Motivation:  
- state clearly what is covered with tests without reading non-trivial code  
- avoid cleverness - tests should be no-brainer  
- get closer to one test case targets one feature/beaviour with one check  
- replace obscure `throw std::exception` with diagnostics that are actually  
  useful to pin-point failure cause and location - makes CI logs useful.  
- allow to select and run specific tests  
- make tests maintenance easy, quick and fun  
  
Propose new structure of tests that reflects the previous tests hierarchy,  
but organizes channel tests in `test/channel` directory with test programs  
each covering specific library feature (or set of closely related features).  
  
The refactored tests cover 100% of checks from the old `channel.cpp`,  
plus it refines or adds a bunch more.  
NOTE: old `test/channel.cpp` has not been removed yet.  
  
Common definitions from the single `test/channel.cpp` moved to  
`channel_test_fixtures.hpp` and namespace `boost::gil::test::fixture`:  
  
Classes and typedefs:  
- `do_test` as `fixture::channel`  
- `value_core` as `fixture::channel_value`  
- `reference_core` as `fixture::channel_reference`  
- `packed_reference_core` as `fixture::packed_channel_reference`  
- `packed_dynamic_reference_core` as `fixture::packed_dynamic_channel_reference`  
- `channel_archetype` and relatives to `channel_concepts.cpp` which is compile  
   test in Jamfile  
- `test_packed_channel_reference()` parts as `fixture::packed_channels565`  
- `test_packed_dynamic_channel_reference()` parts as `fixture::packed_dynamic_channels565`  
  
Test case functions called from `do_test<T>::test_all`:  
- `test_channel_invert()` to `algorithm_channel_invert.cpp` suite  
- `test_channel_convert()` to `algorithm_channel_convert.cpp` suite  
- `test_channel_multiply()` to `algorithm_channel_multiply.cpp` suite  
- `test_channel_math()` split to `algorithm_channel_relation.cpp`  
   and `algorithm_channel_arithmetic.cpp`  
  
Add test cases for each channel value type T as used to run from  
`test_channel_value_impl<T>`, `test_packed_channel_reference<T>` and  
`test_packed_dynamic_channel_reference<T>`.  
  
Add list of possible T-s defined as type-lists `fixture::channel_byte_types`,  
`fixture::channel_integer_types`, `channel_float_types` and  
`channel_bitfield_types` which used with `BOOST_AUTO_TEST_CASE_TEMPLATE`  
generate all possible combination of inputs.  
  
Add new `channel_test_fixture.cpp` is a self-test suite verifying the fixtures.  
  
### Additional notes  
  
- I tried to apply the 90-characters line length limit  
- I was brave to replace members naming using leading underscore (`_min_v`) with trailing underscore (`min_v_`) - the former is too close to `__min_v` which C++ reserves for implementation-specific names.  
- I replaced `typedef` with `using`, preferred `auto`, and some other C++11-s  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-04-28 00:22:01 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385124318  

Just a quick note (as I don't have time to do a full review right now): I understand that this patch helps you debug the current blocking issues, so it's useful to have that. But I'd prefer not to merge it at this time, for a couple of reasons:  
1) it introduces its own risks (associated with each refactoring)  
2) it binds us even more to Boost.Test, when I thought we were trying to move away from it  
  
I'd *really* prefer not to do any further refactoring before the Big Merge, if we can avoid it at all.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-04-28 07:31:58 UTC  
> Updated_at: 2018-04-28 07:50:25 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385149055  

Since the mysterious bugs come and go with existing code edits (eg. @chhenning tests on list with and without std::cout), I simply refactored also with purpose to see if different implementation of the same 'logic' is going to make a difference. Plus, all reasons I listed as related to future direction.  
  
Your comment on Boost.Test is hugely confusing. I think we have discussed that and agree to use it (and the lightweight test). What testing framework do you propose to use? Finally, since this does not remove the existing test, it does not change any features but adds more tests (think of a different angle at the same testing target) , I don't understand what risks you have in mind. It's not different than adding new test case and adding new tests is rather encouraged isn't it.  
  
Which test framework do you @stefanseefeld and @chhenning want to use? Although I think Boost is already there (GIL is Boost) and introducing external deps will simply introduce new hurdle for testers and contributors, I can switch my PR if you both specify to what.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-04-28 12:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385172845  

Sorry if I misremembered our previous discussion on the topic, but I seem to recall us discussing specifically the use of `boost/core/lightweight_test.hpp` to avoid the full fledged Boost.Test dependency. Am I wrong ?  
(As far as alternatives are concerned, I'm starting o like Google Test quite a bit. But that would be a separate discussion...)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-04-28 12:59:16 UTC  
> Updated_at: 2018-04-28 13:00:05 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385174058  

From our discussion in end of March, emails between three of us:  
  
```  
Options we've got:  
  
1. stick to Boost.Test, master it to achieve what we want  
  
    + fully-featured framework, it has it all we ever may need  
  
    - debugging tests may be annoying  
  
2. switch to lightweight_test and compromise  
  
    + regular main-powered program  
  
    - very basic, we may end up creating custom command line handling, etc.  
  
3. Combination of 1 and 2: keep major tests based on Boost.Test; allow lightweight_test for small per-bugfix tests   
  
    + isolated, easy to debug small standalone test programs for hard bugs eg. like the checksum or channel bugs we are on now: all MWE reproducing those bug shold become test cases, and they could be standalone programs  
  
    - diversity of used frameworks may be confusing, need clear guidelines on when to use which framework      
  
4. switch to GTest or Catch2  
  
    + some of us may prefer one or another  
  
    - exotic for a Boost library  
  
    - extra dependency, barrier for potential contributors  
  
  
  
Given the feedback so far, my vote is for 3 or 1  
  
  
I agree. Let's not make this too formal,  
  
```  
  
The last sentence is your answer, If I remember.   
  
  
And in this particular case, I went for Boost.Test, option 3, since the lightweight_test does not offer features necessary to generate test cases from type-lists and keep the tests compact - the original channel.cpp is quite complex and unfolding it to clear structure requires some features from test framework.  
  
Note, there is no problem to use Boost.Test as header only library.  
  
GTest seems like a decentralised alternative, but I preferred to stick with Boost ecosystem and avoid external dependencies as that in fact would even lower chance for this PR to be accepted :)  
  
If you and @chhenning die hard for GTest and if gtest offers needed features, I can try to port to it, though I would prefer not to if it was me to decide,at least not for now. I don't object gtest. I just think any external dependency is an obstacle and Boost.GIL has been defacto abandoned for long enough that it doesn't need to make contributors life harder than necessary.  
  
Please, Christian & Stefan, tell me what's your consensus about 1) test framework 2) this PR

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2018-04-28 13:06:34 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385174614  

@mloskot thanks for the summary of our previous discussion. I fully agree: Moving to Google Test is entirely out of scope, at least for now. And OK, fair enough: we already are using Boost.Test, and your refactoring is indeed a huge improvement, so let's move forward with it. I remain skeptical with respect to Boost.Test (and I don't particularly like NIH-style arguments; in particular in light of my continued push for more modularity, but that, too, is a separate discussion :-) ).  
But for now, I agree and support this PR. Now let's wait for @chhenning to give his go-ahead then we can merge it and move on.  
Thanks !

---

## Comment 6

> Username: chhenning  
> Created_at: 2018-04-28 13:46:19 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385177096  

Let's just stick with 1). It's all we need for now and my believe is that we should spend our energy on more interesting things than unit tests! :-)

---

## Review 7 [Approved]

> Username: chhenning  
> Created_at: 2018-04-28 13:48:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/86#pullrequestreview-116137421  

Good stuff!

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-04-28 14:40:12 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385180869  

@stefanseefeld   
> I remain skeptical with respect to Boost.Test   
  
I am not Boost.Test enthusiast either. I'm just trying to be pragmatic.  
Besides, refactoring tests from framework X to Y is often almost a semi-automatic process, so it should not be a problem to switch in future - I will refactor again :)  
  
@chhenning   
> we should spend our energy on more interesting things than unit tests  
  
Yes, on unit tests, but we should spend substantial energy on tests.  
IMHO, we should forbid any commit that is not cover with a test (not saying unit test, just a test :-)).  
  
Thanks for accepting the PR!

---

## Comment 9

> Username: stefanseefeld  
> Created_at: 2018-04-28 15:48:25 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385185563  

On 28.04.2018 10:40, Mateusz Loskot wrote:  
> @chhenning <https://github.com/chhenning>  
>  
>     we should spend our energy on more interesting things than unit tests  
>  
> Yes, on unit tests, but we should spend substantial energy on tests.  
>  
  
... in particular while we are trying to identify and fix a bug that's  
blocking a milestone ! :-)

---

## Comment 10

> Username: mloskot  
> Created_at: 2018-04-28 18:22:00 UTC  
> Updated_at: 2018-04-28 18:23:13 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385196103  

I second @stefanseefeld   
  
BTW, some interesting results start to bubble up on CircleCI workflow targetting 18 versions :-) of GCC/clang  
  
In the latest workflow following merge of this PR - https://circleci.com/workflow-run/1c075e14-c65f-482e-842c-3e23fb76da8d - two builds are failing  
- **gcc 4.7** - https://circleci.com/gh/boostorg/gil/745 - compilation errors likely due to missing `typedef` or too old compiler, so let's ignore this one for now.  
- **clang 5.0** - https://circleci.com/gh/boostorg/gil/766 - here we get detailed failure:  
  
```  
Running 30 test cases...  
libs/gil/test/channel/algorithm_channel_invert.cpp(22): error: in "channel_value<int>": check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
libs/gil/test/channel/algorithm_channel_invert.cpp(22): error: in "channel_reference<int>": check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
libs/gil/test/channel/algorithm_channel_invert.cpp(22): error: in "channel_reference_const<int>": check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
  
*** 3 failures are detected in the test module "test_algorithm_channel_invert"  
```  
  
Now, this might be actual reproducing of failures on the same **clang 5.0** from older workflows - https://circleci.com/workflow-run/42b255d9-5f6b-4023-9965-c52d605a811e - where the _'old'_ `test/channel.cpp` test failure is reported as this pretty much _useless_  
  
```  
testing.capture-output bin.v2/libs/gil/test/channel.test/clang-linux-5.0.1/release/channel.run  
====== BEGIN OUTPUT ======  
std::exception  
  
EXIT STATUS: 1  
```  
  
Now, **that** looks like a decent score for the new refactored tests, doesn't it :-)

---

## Comment 11

> Username: stefanseefeld  
> Created_at: 2018-04-28 18:31:07 UTC  
> Url: https://github.com/boostorg/gil/pull/86#issuecomment-385196698  

On 28.04.2018 14:22, Mateusz Loskot wrote:  
  
[...]  
> ||  
>  
> Now, *that* looks like a decent score for the new refactored tests,  
> doesn't it :-)  
>  
  
It does indeed. Well done ! :-)  
  
Stefan  
  
--   
  
      ...ich hab' noch einen Koffer in Berlin...

---
