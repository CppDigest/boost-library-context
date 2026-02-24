# #37 use boost filesystem streams in wave to enable utf-8 paths in includes [Merged]

> Username: rlenhardt  
> Created at: 2018-10-04 15:51:13 UTC  
> Updated at: 2018-10-17 13:35:52 UTC  
> Merged at: 2018-10-05 13:15:08 UTC  
> Closed at: 2018-10-05 13:15:08 UTC  
> Url: https://github.com/boostorg/wave/pull/37  

This is the PR for issue #32.  
The first commit changes standard streams to boost streams in the wave lib and the wave driver. I left out the samples.  
The second submit adds 4 tests, two for checking that include paths containing UTF-8 characters work in include directives and passed via commandline include paths, the two other check that those characters fail when not switching to the boost filesystem utf8 codecvt -- at least on windows, they might not fail on OSs that always use UTF8 for path encoding.  
  
I added a new flag for the special directives to switch to the UTF8 codecvt on a testcase basis. I hope the special include dir does not cause problems when checking out via git.

---

## Review 1 [Commented]

> Username: rlenhardt  
> Created_at: 2018-10-04 15:55:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/37#pullrequestreview-161685716  

📁 test/testwave/testwave_app.cpp

```diff
1043 |         {
1044 |+             std::string full(*cit);
1045 |+             got_expected_result(ctx.get_current_filename(),"",full);
```

> Username: rlenhardt  
> Created_at: 2018-10-04 15:55:38 UTC  
> Updated_at: 2018-10-04 15:55:39 UTC  
> Url: https://github.com/boostorg/wave/pull/37#discussion_r222728003  

I (ab)used the got_expected_results function to make the include paths into full paths via the $P placeholder.

> Username: hkaiser  
> Created_at: 2018-10-05 13:13:36 UTC  
> Url: https://github.com/boostorg/wave/pull/37#discussion_r223000646  

Fair enough.


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2018-10-05 13:15:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/37#pullrequestreview-162028282  

Let's see what the tests say. Thanks!

---

## Comment 3

> Username: hkaiser  
> Created_at: 2018-10-06 01:00:56 UTC  
> Url: https://github.com/boostorg/wave/pull/37#issuecomment-427534012  

Things are failing: https://travis-ci.org/boostorg/wave/jobs/437812242#L1039. Could you have a look, please? We could enable the UTF8 changes conditionally...

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-17 13:35:52 UTC  
> Url: https://github.com/boostorg/wave/pull/37#issuecomment-430630770  

As we suspected, UTF-8 paths in the repo are proving problematic, see https://github.com/boostorg/boostdep/issues/8. I'm fixing that by putting the path into a .zip file and extracting it in .travis.yml and appveyor.yml. This solution won't work here though as it's part of the test suite.

---
