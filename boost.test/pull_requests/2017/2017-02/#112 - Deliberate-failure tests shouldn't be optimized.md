# #112 Deliberate-failure tests shouldn't be optimized [Closed]

> Username: aargor  
> Created at: 2017-02-14 20:11:01 UTC  
> Updated at: 2017-12-29 19:44:19 UTC  
> Closed at: 2017-12-29 19:44:19 UTC  
> Url: https://github.com/boostorg/test/pull/112  

A few tests designed to deliberately fail appeal to UB (undefined  
behavior), but as the MSVC optimizer improves we will take advantage of  
that to remove UB statements. In order for deliberate failures to occur,  
the tests must have the optimizer turned off.

---

## Comment 1

> Username: aargor  
> Created_at: 2017-02-14 20:13:34 UTC  
> Updated_at: 2017-03-21 18:42:13 UTC  
> Url: https://github.com/boostorg/test/pull/112#issuecomment-279822110  

Hello, I'm from the MSVC++ compiler team. I created the above pull request because as we're improving the optimizer, we've tested internally that a few tests will fail as we more successfully optimize in the presence of undefined behavior. The team I'm on expects that these tests will fail with the soon-to-be released VS2017, because we optimize away the code that's designed to trigger the faults.  
  
I hope this is the right way to contribute back to the Boost project. Please let me know if there's anything else I should do.  
  
Thanks,  
Aaron Gorenstein  
Microsoft Visual C++  
  
Edit: I'll add that I've made a similar pull-request to boostorg/test.  
Edit2: I meant boostorg/context in the first edit.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-03-18 13:48:14 UTC  
> Url: https://github.com/boostorg/test/pull/112#issuecomment-287547439  

Hi,  
  
Since you released VS2017, is this PR still relevant? If yes, I would welcome a version of the compiler be used together with the `BOOST_MSVC` macro.

---

## Comment 3

> Username: aargor  
> Created_at: 2017-03-21 18:41:36 UTC  
> Url: https://github.com/boostorg/test/pull/112#issuecomment-288178797  

Hi,  
  
Yes, I've just confirmed that the patch is needed with the latest compiler version. Thanks for the feedback! I think I am now using the BOOST_MSVC macro as you requested (and I've confirmed this still addresses the reported issues), but let me know if you were thinking of something else.  
  
(For concreteness, in VS2017 _MSC_VER is 1910, and VS2015 it's 1900. My understanding is that BOOST_MSVC inherits that value.)  
  
Thanks again,  
Aaron

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2017-12-25 15:55:58 UTC  
> Url: https://github.com/boostorg/test/pull/112#issuecomment-353877323  

In the test queue, thanks

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2017-12-29 19:44:19 UTC  
> Url: https://github.com/boostorg/test/pull/112#issuecomment-354491857  

Merged to develop, thanks.

---
