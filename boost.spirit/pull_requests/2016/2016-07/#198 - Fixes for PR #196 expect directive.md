# #198 Fixes for PR #196 expect directive [Merged]

> Username: mxc-commons  
> Created at: 2016-07-19 15:16:56 UTC  
> Updated at: 2016-07-22 05:06:36 UTC  
> Merged at: 2016-07-22 05:06:36 UTC  
> Closed at: 2016-07-22 05:06:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/198  

his PR contains fixes for both issues reported by Lastique and  
MarcelRaad. It makes PR #197 obsolete.  
  
Removed obsolete dependency  of test/qi/expectd.cpp on  
mxc/qitoo/qitoo.hpp. Was reintroduced by mistake.  
  
Created qi/detail/expectation_failure.hpp. It defines the  
expectation_failure template as a common dependency of expect directive  
and expect operator. Removed expectation_failure from  
operator/expect.hpp.  
  
Changed dependency of debug_handler.hpp to from operator/expect.hpp to expectation_failure.hpp.  
  
Changed version.hpp to 2.54. I did not notice that addl file in the  
first place.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-07-19 15:29:44 UTC  
> Updated_at: 2016-07-19 17:06:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71362263  

Not a biggie, but you probably shouldn't claim copyright on a file with contents lifted elsewhere. The basic rule here is that you add your copyright if there's significant contribution to the code in terms or actual content.

---

## Comment 2

> Username: mxc-commons  
> Created_at: 2016-07-19 15:37:38 UTC  
> Updated_at: 2016-07-19 17:06:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71363893  

Was to fast with copy & paste. Fixed. Sorry.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-07-19 16:54:42 UTC  
> Updated_at: 2016-07-19 17:06:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71378564  

Is an include for `info` missing?

---

## Comment 4

> Username: mxc-commons  
> Created_at: 2016-07-19 17:09:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71380911  

That was not really a question, was it? :) You are perfectly right.  
  
I believe, I have to slow down things again for the sake of quality. I went into supersonice mode when I saw how fast #197 was merged. I apologize.

---

## Comment 5

> Username: djowel  
> Created_at: 2016-07-19 22:19:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71435105  

Can you run all tests before a PR, preferably with at least two compilers?

---

## Comment 6

> Username: mxc-commons  
> Created_at: 2016-07-19 22:57:18 UTC  
> Updated_at: 2016-07-19 23:05:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71439415  

After that issues showed up, I ran the build on the whole spirit test-suite. Before I did run on expect directive only in order to detect c++11-isms.  
  
I found that the test suite does not build without errors on either toolset msvc-9.0, msvc-12.0 and msvc-14.0. The expect directive's tests pass on those three. The errors are not related to the expect directive and some look quite serious. I seem not to be able to attach the output files here, so I will send them to the mailing list.  
  
At the moment I only have the several MS toolsets installed. I understand that I will have to provide non-MS toolsets as well. I will do.

---

## Comment 7

> Username: Lastique  
> Created_at: 2016-07-20 08:12:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71482394  

I believe, Boost.Spirit doesn't have an includability test (a test that includes a single header to check that the header is self-sufficient; the test is run for all public headers), so missing includes can go unnoticed.

---

## Comment 8

> Username: mxc-commons  
> Created_at: 2016-07-21 03:21:21 UTC  
> Updated_at: 2016-07-21 03:44:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71641841  

The test suite does not build without compilation errors or failed tests (!) with  gcc 6.1.0 (MinGW) either. This applies to the develop branch. I can provide an output file if it is of interest. gcc complains (almost) about the same things my several msvc toolsets do.  
  
expectd.cpp test compiles and passes. Bu is that meaningful at all?  
  
Btw, the examples do not compile as well. I can provide on output file if anyone is interested.  
  
@Lastique: Your beliefs are right.

---

## Comment 9

> Username: mxc-commons  
> Created_at: 2016-07-22 05:04:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/198#discussion_r71827350  

Can I close this PR?

---
