# #258 Improve testing of util [Closed]

> Username: sdebionne  
> Created at: 2015-03-10 22:18:26 UTC  
> Updated at: 2015-03-12 10:17:24 UTC  
> Closed at: 2015-03-11 17:34:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/258  

This PR aims to improve the testing of the util part of the library.  
- Use the pre-build UTF when building with b2 (default remains inline UTF) to improve compile time.  
- Adapt Jamfile to use the `unit-test` rule rather than the deprecated `test-suite` rule to improve error reporting.  
- Make every test suites a separate target so that, for example, `b2 range` will build and run the range test suite only.  
- Use BOOST_TEST_MODULE, BOOST_AUTO_TEST_SUITE and BOOST_AUTO_TEST_CASE to better identify the tests and improve the test report.  
- Add unused but valid tests for variant (compress_variant, transform_variant)  
- Remove unused (not in the Jamfile) tests (as_range and is_implemented).

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-11 06:13:00 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190896  

Why do you need this `using` command? I cannot see where it is used.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-11 06:13:24 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190906  

Same here.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-11 06:14:15 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190932  

Why did you make this change? AFAIU, what was written was consistent with our coding standards.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-11 06:14:30 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190938  

Same as above.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-11 06:15:28 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190972  

Fix spaces here: `bg::model::point<double, 3, bg::cs::cartesian>`.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-11 06:15:59 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26190981  

Same comment here.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-03-11 06:18:06 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26191048  

I am pretty sure `>>` will cause problems in with compilers compiling in C++03 mode.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-03-11 06:19:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78211371  

Hi Samuel. Thanks for this PR.  
Please see my initial comments. I would like to make a second pass later on.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-03-11 06:20:40 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26191099  

There are also other instances in other places, about the same issue; I have not commented on them.

---

## Comment 10

> Username: sdebionne  
> Created_at: 2015-03-11 08:07:29 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26194159  

That's my mistake, this is used only when manually registering test suites / cases. It should be removed here.

---

## Comment 11

> Username: sdebionne  
> Created_at: 2015-03-11 08:11:08 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26194283  

Again my mistake, or rather an MSVC 2013 annoying feature that reformat things without user's consent...

---

## Comment 12

> Username: sdebionne  
> Created_at: 2015-03-11 08:16:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78221082  

There are many small formatting issues that were introduced by my editor (MSVC 2013) that were not intended and I'm going to fix that right now. I have adjusted the option so that it won't happen again.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-03-11 08:18:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78221254  

@sdebionne Okay, understood. Thanks!

---

## Comment 14

> Username: sdebionne  
> Created_at: 2015-03-11 09:18:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78228619  

Other parts of the library use the Test Execution Monitor (TEM) with the test tools but not the UTF. Both cannot be included (else test_main would be defined twice). I have introduced a  new macro `BOOST_GEOMETRY_TEST_USE_UTF`  that includes UTF (now used by util). TEM is included by default when the macro is not defined so that other tests are not broken.  
  
I have also checked that the util tests can be compiled easily without Boost Build (using the inline UTF), e.g.:  
  
```  
cl /EHa /I"BOOST_ROOT" /I"BOOST_ROOT\boost\libs\geometry\test" test\util\range.cpp  
```  
  
compile (and run) sucessfully. That's rather handy to debug tests (and a good use case for the inline UTF) !

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-03-11 09:20:40 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26197570  

Could you please add a new line at the end of the file? Thanks.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-03-11 09:20:59 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26197581  

Same here.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-03-11 09:22:29 UTC  
> Updated_at: 2015-03-11 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26197665  

Since this is a header file you need also guard it an appropriate `#ifndef/#define/#endif` block.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-03-11 09:26:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78229641  

We should wait for Barend to take a look at this PR. He has strong opinions about how testing should be done, and we should wait for his opinion on this PR.

---

## Comment 19

> Username: sdebionne  
> Created_at: 2015-03-11 09:44:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78232229  

OK. I have fixed the error you reported in the latest code review. An other benefit of using the `unit-test` rule that I did not mentioned in the introduction is that only the tests that do not passed are build / run when invoking `b2`.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-03-11 09:48:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78232783  

Thanks for fixing the errors.  
What you describe about `b2` is already happening for me (on Ubuntu linux) with our existing testing framework. Maybe things are different on windows.

---

## Comment 21

> Username: sdebionne  
> Created_at: 2015-03-11 10:53:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78241956  

I'm probably wrong about the build / run, I was under the impression that the `run` rule always run, but the documentation says otherwise. Probably a configuration problem on my side.  
  
A side note on running the tests under Windows:  
- `b2 headers` does not generate the prepossessed files for Boost.MPL, Boost.Preprocessor and others libs, these files must be "added" manually (or a release distribution of Boost must be included as well).  
- b2 should be run with `--hash`. Some tests (e.g. spherical_equatorial_custom_point_with_wrong_units) exceed the max length of the path for the generated .rsp file (even with `--abbreviate-paths`). It's ok if boost is in a drive root directory though.  
  
These information may be added to the Contribution Tutorial, no ?

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-03-11 14:40:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78275844  

How does it improve the testing?  
The majority of the unit tests in Boost.Geometry doesn't use automated test framework but the minimalistic one. The advantage of it is that we doesn't use built library to test the code. AFAIK this is the main reason why the tests are implemented as they are. Why should we change it?

---

## Comment 23

> Username: sdebionne  
> Created_at: 2015-03-11 16:34:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78300805  

@awulkiew Let me try to plead the case ! I believe it improves the testing (not the test coverage) on several fronts:  
On the build system front:  
- the use of the built UTF improves the compile time (about 2x faster on my machine). Note that, in my proposal, it is used only when running tests with Boost.Build while keeping the old behavior (header only tests) otherwise.  
  
From Boost.Test documentation:  
  
> **Prefer offline compiled libraries to the inline included components**  
> If you are just want to write quick simple test in environment where you never used Boost.Test before - yes, use included components. But if you plan to use Boost.Test on permanent basis, small investment of time needed to build (if not build yet), install and change you makefiles/project settings will soon return to you in a form of shorter compilation time. Why do you need to make your compiler do the same work over and over again?  
  
On the test front, the use of UTF gives the functionality of a full framework.  
- test cases allow to better identify the failing tests  
- single test case can be executed  
- using automatic register test case / template test case / suite is handy.  
- flexibility for the reporting (format, output) event if Boost.Regression does not use it.  
- configurable at runtime, for instance `b2 testing.arg="--log_level=test_suite"` let your change the UTF log level, but any [runtime parameter](http://www.boost.org/doc/libs/release/libs/test/doc/html/utf/user-guide/runtime-config/reference.html) can bet set.  
  
Now it is, of course, up to you to decide if it is worth the transition !

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2015-03-11 17:03:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78307216  

Please keep all tests header-only.

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2015-03-11 17:29:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#discussion_r26234585  

Sorry, all this cannnot be accepted.  
Maybe it is a good idea to first ask on the list if this is a good idea, and then submit a PR. That avoids creating the code, the PR and large discussions

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2015-03-11 17:33:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78313550  

So, in summary, please close this request, don't merge.  
And it looks like util is the first of a range, but don't do that, please keep all tests header only such that all existing IDE's (and there are many) keep working for all tests.

---

## Comment 27

> Username: awulkiew  
> Created_at: 2015-03-11 17:38:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78314604  

@barendgehrels The test would still be header-only if compiled without `BOOST_GEOMETRY_TEST_BUILD_WITH_B2` defined. They'd be similar to the tests of setops or distance implemented by Menelaos. Btw, I'd name this macro somehow differently since one could choose to build the Boost.Test manually locally, not neccessarily use it only with b2.

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2015-03-11 17:41:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78315376  

@awulkiew No it won't compile because the header is changed to util/common.hpp which defines that define by itself

---

## Comment 29

> Username: barendgehrels  
> Created_at: 2015-03-11 17:43:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78315643  

BTW I did not know before I could close it, but I can. I did to avoid pressing the merge button accidentally for this PR, which is unwished

---

## Comment 30

> Username: mkaravel  
> Created_at: 2015-03-11 17:44:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78315848  

@barendgehrels I think having the macro there is wrong, but we can still support the existence of the macro if somebody wants to build manually.  
I would not have it there by default in Jamfiles.

---

## Comment 31

> Username: awulkiew  
> Created_at: 2015-03-11 17:48:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78316868  

@barendgehrels AFAIU then `#include <boost/test/included/unit_test.hpp>` will be included, so a header-only version of automated test suite. Like in the tests of setops and distance that we have already.  
  
**b2** will always require building Test and linking against it. Is that a problem?  
  
I think that the idea is good in general. The ability of running all of the tests 2x faster and in the same time allowing to build them manually without the need to link against Test.  
  
Though I also agree that it'd be wise to talk about it on the list first :)

---

## Comment 32

> Username: barendgehrels  
> Created_at: 2015-03-11 17:48:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78316968  

The setops/distance tests are still the tests which are already yellow for half a year or more in the regression tests, in some platforms. This was supposed to be temporary, but apparently it never gets fixed. I want to see everything green there, or as much as possible. So that is another reason to oppose to changes like this.

---

## Comment 33

> Username: barendgehrels  
> Created_at: 2015-03-11 17:49:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78317275  

@awulkiew  yes linking against test that is the big problem, I mentioned that earlier, and I've still the same opinion, and that will not change even if building is a little faster

---

## Comment 34

> Username: mkaravel  
> Created_at: 2015-03-11 17:51:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78317875  

@barendgehrels AFAIU this is only for MSVC 8.0 and only on the `develop` branch.  
Plus this is not our fault but it has to do with Boost.Typeof.  
I think that Boost.Typeof is not properly maintained. I have a PR for the fix, I have contacted the Boost Maintenance Team, and nothing has happened.

---

## Comment 35

> Username: mkaravel  
> Created_at: 2015-03-11 17:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78318274  

I agree with Barend about the IDE argument: it puts one more burden if you need to set up Boost.Test to play along nicely.  
If I were to do something, it would be to allow for building with the pre-compiled UTF, but only through macros, not defined by default.

---

## Comment 36

> Username: barendgehrels  
> Created_at: 2015-03-11 17:56:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78321429  

@mkaravel Sure, I know the error lies elsewhere, and that you did your best to fix it. Yes, good to mention that here too indeed.  
  
But as a new PR comes in to change to that same behaviour, I thought it was good to describe that situation too

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2015-03-11 18:05:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78327421  

Also, why:  
"Remove unused (not in the Jamfile) tests (as_range and is_implemented)."  
  
is just removed? Of course something should be done, but not just removing it. The source is moved from util to algorithm/detail, and so need the unit test to be moved too.  
Anyway, just did that (by looking for it), so no need for action

---

## Comment 38

> Username: awulkiew  
> Created_at: 2015-03-11 18:08:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78329212  

@barendgehrels Why linking against Test done by b2 is problematic? Does it interfere with your workflow somehow?

---

## Comment 39

> Username: barendgehrels  
> Created_at: 2015-03-11 18:14:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78333349  

Yes, it interfers with my workflow in all my environments and IDE's. I don't use b2, only for running the unit test itself.  
Besides that, I don't want Boost.Geometry to be dependent on b2, it should run everywhere, on any compiler / IDE / platform without any effort. So also the unit tests because that is our primary platform in this TDD environment.

---

## Comment 40

> Username: sdebionne  
> Created_at: 2015-03-11 20:16:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78361614  

Shall I put a new PR for these changes:  
- Add tests for variant (compress_variant, transform_variant) that are missing in the Jamfile  
- Remove tests (as_range and is_implemented) that are not in the Jamfile and feature were removed from the library  
  
About discussing new functionality, IMHO, when a prototype exists, it's easier to review on GitHub...

---

## Comment 41

> Username: barendgehrels  
> Created_at: 2015-03-11 21:13:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78373117  

Thanks!  
  
Note my remark above: as_range is not removed from the library. The headerfile was just moved. I moved the unit-test too so that should be OK now.   
  
About is_implemented, that feature is most probably never, or not yet finished. @mkaravel  it is referenced by distance_result in a comment, are there any unfinshed plans for this or can the unit test be removed? If so we can also remove the comment.  
  
About new functionality, yes it might be convenient. As you prefer - I always regret to reject a PR on which people did spend time.  
  
But anyway, thanks for your efforts to work on this.

---

## Comment 42

> Username: sdebionne  
> Created_at: 2015-03-12 08:01:48 UTC  
> Updated_at: 2015-03-12 08:02:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78437681  

About is_implement, it's the merge of one my PR that added it. It should never have been there in the first place, so safe to remove.  
  
I have started a discussion on the mailing with an other proposition to implement runtime reporting (vs compile time) of nyi feature.  
  
There is an other option  to speed up test compile time: precompiled headers. This would be helpful for supported toolsets (gcc, msvc and probably clang) and completely transparent for dev that don't used b2. An include directive shoul be added to every source file though. I'm going to give it a shot and report.

---

## Comment 43

> Username: sdebionne  
> Created_at: 2015-03-12 09:01:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78443956  

Precompiled header does not work because the included version of the unit test library is missing `inline` for every functions resulting in duplicated symbols (even if the header files have a guard against multiple inclusion). Probably a side effect of precompiled headers.

---

## Comment 44

> Username: barendgehrels  
> Created_at: 2015-03-12 10:06:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78452677  

We can report that to the Boost.Test team, it is an error to omit inline in free functions in headerfiles.

---

## Comment 45

> Username: sdebionne  
> Created_at: 2015-03-12 10:17:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/258#issuecomment-78454069  

The included and linked version share the same code base. The fix would require to define a BOOST_TEST_INLINE macro that would be defined to `inline` for the included version and empty otherwise,  and use it for free function, but also member function defined outside class. Unfortunately, that does not sound like a fast & easy job...

---
