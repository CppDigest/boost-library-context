# #6 rvalue reference, perfect forwarding and variadic template support [Merged]

> Username: VemundH  
> Created at: 2014-09-07 14:37:02 UTC  
> Updated at: 2018-10-15 18:25:24 UTC  
> Merged at: 2018-10-08 02:45:02 UTC  
> Closed at: 2018-10-08 02:45:02 UTC  
> Url: https://github.com/boostorg/assign/pull/6  

Adds rvalue, perfect forwarding and variadic templates.  
Variadic templates replaces boost.preprocessor (only for compilers with both rvalue and variadic templates support). This removes the max argument limitation (BOOST_ASSIGN_MAX_PARAMS).  
  
A change was required in assign_decay (related to this boost::decay issue: https://svn.boost.org/trac/boost/ticket/7760).

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-07-27 11:57:40 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-408397668  

If you could rebase this on the latest develop and resolve the merge conflict, then we'll get a CI build out of it. Thanks.

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2018-07-27 12:01:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assign/pull/6#pullrequestreview-141084806  

I see no tests added as part of these changes.  Do you believe the standard set of tests using C++03 and C++11 modes with the pre-existing test code is sufficient?  We'll see, once you rebase on master and get a CI build with code coverage.

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2018-07-31 14:44:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/assign/pull/6#pullrequestreview-141990614  

clang-5.0 cxxstd=11 fails, see:  
https://api.travis-ci.org/v3/job/409990297/log.txt

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-08-03 16:45:44 UTC  
> Updated_at: 2018-08-04 18:13:14 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-410311167  

# [Codecov](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=h1) Report  
> Merging [#6](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/assign/commit/308b6a4c6bcaf569f8e43131f42b04adc1b83cd7?src=pr&el=desc) will **increase** coverage by `2.08%`.  
> The diff coverage is `61.11%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/assign/pull/6/graphs/tree.svg?height=150&width=650&token=SOGsyPqaS7&src=pr)](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop       #6      +/-   ##  
===========================================  
+ Coverage    76.53%   78.62%   +2.08%       
===========================================  
  Files           13       13                
  Lines          260      262       +2       
  Branches        55       55                
===========================================  
+ Hits           199      206       +7       
+ Misses          10        5       -5       
  Partials        51       51  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/assign/std/queue.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL3F1ZXVlLmhwcA==) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/std/stack.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL3N0YWNrLmhwcA==) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/ptr\_map\_inserter.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vcHRyX21hcF9pbnNlcnRlci5ocHA=) | `80% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/list\_inserter.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vbGlzdF9pbnNlcnRlci5ocHA=) | `87.14% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/std/list.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL2xpc3QuaHBw) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/std/deque.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL2RlcXVlLmhwcA==) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/std/set.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL3NldC5ocHA=) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/std/vector.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vc3RkL3ZlY3Rvci5ocHA=) | `50% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/ptr\_list\_inserter.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vcHRyX2xpc3RfaW5zZXJ0ZXIuaHBw) | `72.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/assignment\_exception.hpp](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vYXNzaWdubWVudF9leGNlcHRpb24uaHBw) | `66.66% <0%> (+66.66%)` | :arrow_up: |  
| ... and [3 more](https://codecov.io/gh/boostorg/assign/pull/6/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=footer). Last update [308b6a4...62c23a9](https://codecov.io/gh/boostorg/assign/pull/6?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: VemundH  
> Created_at: 2018-08-04 18:26:03 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-410468521  

A long time since I created this pull request.  
Added tests for std::array support (the only new feature). The rest is alternative code paths for compilers with C++11 support, and the standard set of tests should be sufficient. I also believe the recently disabled tests on C++11 can be re-enabled with this.

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-08-21 02:31:26 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-414529735  

The build failure is erroneous - everything passed.

---

## Comment 7

> Username: morinmorin  
> Created_at: 2018-10-13 14:25:19 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429546357  

This PR breaks old GCC (<= 4.6) in C++11 mode. For example,  
this simple code fails to compile  
```  
#include <boost/assign.hpp>  
```  
  
I don't think it's sensible to use C++11 with old GCC, but IMHO "just #including `<boost/assign.hpp>` makes compilations fail" would be a severe breakage. A fix is to change  
```  
`#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
```  
to  
```  
#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES) \  
 || BOOST_WORKAROUND(BOOST_GCC, < 40700)  
```

---

## Comment 8

> Username: morinmorin  
> Created_at: 2018-10-13 14:26:56 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429546491  

This branch (L506 and L670)  
```  
#elif !defined(BOOST_NO_CXX11_HDR_INITIALIZER_LIST) && !defined(BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS)  
        template< class Container,  
            class = typename boost::disable_if< is_initializer_list<Container> >::type  
        >  
        operator Container() const  
```  
needs to SFINAE out `Allocator` (e.g. by adding template parameter `typename Dummy = typename Container::iterator`) to fix [Trac-7364](https://svn.boost.org/trac10/ticket/7364).

---

## Comment 9

> Username: morinmorin  
> Created_at: 2018-10-13 14:29:19 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429546655  

Forgot to say the above comments are about `<boost/assign/list_of.hpp>`.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-10-13 14:53:59 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429548301  

> This PR breaks old GCC (<= 4.6) in C++11 mode.  
  
Right.  
  
@jeking3, consider expanding the platform coverage in your Travis scripts, as already mentioned in https://github.com/boostorg/function/issues/21. For legacy libraries, you really need 4.4 and 4.6 jobs to catch pull requests that break them. In addition, for g++ 6 and above you want to test C++14 because that's their default and that's what everyone would be using. Your testing g++7 in 03/11 mode is decidedly suboptimal.  
  
The minimum coverage I like is f.ex. https://github.com/boostorg/parameter/blob/develop/.travis.yml - g++ 4.4, 4.6, 4.8 (default), 5, 6, 7, 8; clang with and without libc++, Mac. Some of these can in principle be dropped (you could f.ex. test g++5/6 with 11/14, 7 and 8 with 14/17) but the time saved is usually not worth the loss in coverage, unless the library tests take a really long time.  
  
For community-maintained libraries not breaking things that used to work is a priority.

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-10-13 14:58:25 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429548622  

(And for g++ 7 and above, and for later libc++, you want to test C++17 because of standard library removals that may need addressing.)

---

## Comment 12

> Username: jeking3  
> Created_at: 2018-10-13 15:58:09 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429552904  

I'd like the CI builds not to be a full matrix test, because of a couple reasons:  
  
1. We already have a matrix test, albeit that it catches things after the fact.  
2. Our build backlog is getting really long - I submitted some things last night and they are still waiting after 12 hours.  Nobody purchased more slots on Travis to fix this issue.  
  
On the mailing list I thought we had discussed not testing < gcc 4.8 any more, and that folks can happily use older versions of boost if they need to support older compilers or language levels.  At some point the compatibility matrix is not sustainable.  Any commercial entity would have dropped C++03 support on this project a long time ago due to maintenance costs.  
  
I think there is a balance that needs to be made.  Each PR cycle cannot kick off 50 builds which take 10 minutes each.  
  
Were these failures identified in the matrix tests?  How does one compare two matrix tests on a repo from different dates to see what the regressions are?  Is there a tool that can do this, or is it always manual?  
  
These issues found need to be opened as new issues or as PRs.  Commenting here has a chance of getting lost because the PR is merged.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-10-13 16:14:18 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429554115  

If you would rather break things that worked before, you are not a good fit for the CMT. This is not what the CMT is about.

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-10-13 16:20:19 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429554608  

That's not my intention, however the CMT or any other group cannot reasonably support an ever-growing backwards compatibility matrix.  There do have to be reasonable limits.  I will look into beefing up the CI test matrix in the CMT repositories.

---

## Comment 15

> Username: jeking3  
> Created_at: 2018-10-13 17:01:21 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429557917  

@morinmorin isn't that what #20 was for?

---

## Comment 16

> Username: morinmorin  
> Created_at: 2018-10-13 18:07:08 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429562955  

> @morinmorin isn't that what #20 was for?  
  
Yes, that patch contains a fix for issues with `Allocator`. But, since a huge patch (i.e. this PR) got merged, I'm pretty sure PR #20 will not be merged cleanly. So, I recommended throwing out #20 and pinpoint how to fix the new code.

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-10-14 12:45:08 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429623533  

@pdimov check this out - it catches the issues described:  
  
https://travis-ci.org/jeking3/assign/builds/441266324  
  
I'd like to make the Windows build better as well.

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-10-14 14:03:11 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429629036  

Looks good, you have to use 98 for g++ 4.4 though, it doesn't yet have 03. :-)

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-10-14 14:15:11 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429629924  

If you switch UBSAN to g++ 8, you'll cover that as well without spending another job.  
  
All the clangs seem to be using libstdc++ though. You need to have at least one on libc++, whether by adding a Mac job, or by using libc++ on one of the existing Linux ones.

---

## Comment 20

> Username: jeking3  
> Created_at: 2018-10-14 14:22:05 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429630422  

There are even more issues, like many of the clang jobs are still using clang-5.  Still working on it... :)

---

## Comment 21

> Username: pdimov  
> Created_at: 2018-10-14 14:28:52 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429630957  

clang-5 is the default clang++ on Travis at the moment, so that's probably why.  
  
For a minimal matrix, having libc++ is more important for coverage than having many clang versions, so I usually go with one default clang and one clang++-libc++. It's not that earlier clangs don't have their oddities but most libraries don't encounter them.  
  
I sometimes add clang-3.4 because that's the default system clang on Trusty: https://github.com/boostorg/smart_ptr/blob/develop/.travis.yml#L130  
  
but it's really not that critical.

---

## Comment 22

> Username: jeking3  
> Created_at: 2018-10-14 15:21:41 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429635127  

@morinmorin is the detection logic for the macro `BOOST_NO_CXX11_VARIADIC_TEMPLATES` correct for gcc 4.4 and 4.6?

---

## Comment 23

> Username: jeking3  
> Created_at: 2018-10-14 16:12:01 UTC  
> Updated_at: 2018-10-14 16:13:20 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429639086  

@pdimov have you seen anything like this before on osx jobs?  Simple commands like "cd" are failing.  
```  
$ source ci/travis/install.sh  
++++basename /Users/travis/build/jeking3/assign  
+++export SELF=assign  
+++SELF=assign  
+++cd ..  
+++__zsh_like_cd cd ..  
+++typeset __zsh_like_cd_hook  
+++builtin cd ..  
(it just dies a few lines after that)  
```  
https://travis-ci.org/jeking3/assign/jobs/441316951

---

## Comment 24

> Username: morinmorin  
> Created_at: 2018-10-14 16:23:06 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429640072  

> @morinmorin is the detection logic for the macro BOOST_NO_CXX11_VARIADIC_TEMPLATES correct for gcc 4.4 and 4.6?  
  
In some rare cases (mainly for oldish compilers), Boost.Config says FEATURE_XXX is implemented in the compiler even if the implementation is incomplete. This is the case for the variadic template feature on GCC 4.4–4.6.  
Well, both GCC 4.4–4.6 have basic functionalities of variadic templates and so they pass the test in Boost.Config.  
  
For advanced usage like our case, we should use   
```  
#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || BOOST_WORKAROUND(BOOST_GCC, < 40700)  
```  
to detect unsupported compilers.

---

## Comment 25

> Username: jeking3  
> Created_at: 2018-10-14 16:29:46 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429640619  

@morinmorin Is there just one particular block that needs this treatment, or other files in this repo?  
```  
jking@ubuntu:~/boost/libs/assign$ grep -r VARIADIC_TEMPLATES .  
./include/boost/assign/ptr_list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_map_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_map_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_map_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_of.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES)  
./include/boost/assign/list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES)  
./include/boost/assign/list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
./include/boost/assign/ptr_list_inserter.hpp:#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
```

---

## Comment 26

> Username: morinmorin  
> Created_at: 2018-10-14 17:06:34 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429643423  

> @morinmorin Is there just one particular block that needs this treatment, or other files in this repo?  
  
Fixing `<boost/assign/list_of.hpp>` makes this code   
```  
#include <boost/assign.hpp>  
```  
compile fine. So I guess just one file needs the treatment (though I haven't looked into other files).  
  
That said, it might be better to define  
```  
#if !(defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || BOOST_WORKAROUND(BOOST_GCC, < 40700))  
// or #if !(defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || BOOST_WORKAROUND(BOOST_GCC, < 40700) \  
//       || defined(BOOST_NO_CXX11_RVALUE_REFERENCES))  
#define BOOST_ASSIGN_USE_VARIADIC_TEMPLATES  
```  
and use that macro extensively in Boost.Assign.

---

## Comment 27

> Username: pdimov  
> Created_at: 2018-10-14 21:45:11 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429665901  

This change broke the Range tests by the way. https://travis-ci.org/boostorg/range/builds/441323699

---

## Comment 28

> Username: jeking3  
> Created_at: 2018-10-14 21:49:07 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429666156  

Thanks I am working the issue and will roll out CI improvements as far and  
wide as possible.  
  
On Sun, Oct 14, 2018, 5:45 PM Peter Dimov <notifications@github.com> wrote:  
  
> This change broke the Range tests by the way.  
> https://travis-ci.org/boostorg/range/builds/441323699  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/assign/pull/6#issuecomment-429665901>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbfdiHCAem1b0QR7VHt7xDCrt6-6Qks5uk7BngaJpZM4CfXrL>  
> .  
>

---

## Comment 29

> Username: morinmorin  
> Created_at: 2018-10-15 15:18:57 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429896586  

Wow, this PR broke the following code on *all* platforms!  
```  
#include <boost/assign/list_of.hpp>  
```  
Don't worry, this is simply fixed by adding  
```  
#include <boost/config.hpp>  
```  
to `<boost/assign/assignment_exception.hpp>` ;)

---

## Comment 30

> Username: jeking3  
> Created_at: 2018-10-15 17:48:05 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429949068  

@morinmorin I included that in #27

---

## Comment 31

> Username: pdimov  
> Created_at: 2018-10-15 18:25:24 UTC  
> Url: https://github.com/boostorg/assign/pull/6#issuecomment-429962658  

The tests should be fixed to include the Assign header first, "per best practices."  
  
By the look of it, they did start out this way, but then someone added the detail/workaround.hpp include in front, defeating the purpose.

---
