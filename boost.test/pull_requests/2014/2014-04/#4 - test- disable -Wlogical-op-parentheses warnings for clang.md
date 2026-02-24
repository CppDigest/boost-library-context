# #4 test: disable -Wlogical-op-parentheses warnings for clang [Merged]

> Username: timblechmann  
> Created at: 2014-04-04 10:48:24 UTC  
> Updated at: 2014-07-18 10:38:52 UTC  
> Merged at: 2014-04-28 21:16:55 UTC  
> Closed at: 2014-04-28 21:16:55 UTC  
> Url: https://github.com/boostorg/test/pull/4  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-04-04 11:01:09 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-39553446  

Hi Tim,  
  
Thanks for the patch. Is there any trac issue associated to it?  
  
Best,  
Raffi  
  
On 04 Apr 2014, at 12:48, Tim Blechmann notifications@github.com wrote:  
  
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/timblechmann/test topic/fix-warnings  
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/test/pull/4  
>   
> Commit Summary  
>   
> test: disable -Wlogical-op-parentheses warnings for clang  
> File Changes  
>   
> M include/boost/test/detail/enable_warnings.hpp (10)  
> M include/boost/test/detail/suppress_warnings.hpp (12)  
> Patch Links:  
>   
> https://github.com/boostorg/test/pull/4.patch  
> https://github.com/boostorg/test/pull/4.diff  
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 2

> Username: timblechmann  
> Created_at: 2014-04-04 11:05:22 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-39553734  

i didn't check for a trac issue

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2014-04-04 11:49:39 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-39556723  

ok  
On 04 Apr 2014, at 13:05, Tim Blechmann notifications@github.com wrote:  
  
> i didn't check for a trac issue  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 4

> Username: timblechmann  
> Created_at: 2014-04-28 19:53:15 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41604964  

hmm, the topmost line tells me: "timblechmann  wants to merge 1 commit into boostorg:develop  from timblechmann:topic/fix-warnings"

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2014-04-28 22:56:24 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41624506  

Sorry for the late answer.  
  
I am not super fan of silencing the warnings. Fact is that currently I cannot see this kind of warnings because they are flooded by many many others (clang-503/Xcode 5.1), especially for the examples and the tests.   
If the warnings are just about operator precedence, I would prefer fixing the code.  
  
I created an issue for this topic:  
https://svn.boost.org/trac/boost/ticket/9960  
  
Please provide some information in the ticket, in particular the way you compile boost.test. On my machine, the toolset clang should be selected manually.  
  
Best,  
Raffi  
  
On 28 Apr 2014, at 21:53, Tim Blechmann notifications@github.com wrote:  
  
> hmm, the topmost line tells me: "timblechmann wants to merge 1 commit into boostorg:develop from timblechmann:topic/fix-warnings"  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 6

> Username: Belcourt  
> Created_at: 2014-04-29 00:18:37 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41629967  

The issue is a bogus clang compiler warning about operator precedence so there's nothing to fix.

---

## Comment 7

> Username: timblechmann  
> Created_at: 2014-04-29 06:46:53 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41646046  

@superraf-the-real any reason not to discuss this in the PR on github?

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2014-04-29 07:22:37 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41648058  

As you may see below, I do not have your email.  
  
On 29 Apr 2014, at 08:46, Tim Blechmann notifications@github.com wrote:  
  
> @superraf-the-real any reason not to discuss this in the PR on github?  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2014-04-29 08:56:43 UTC  
> Url: https://github.com/boostorg/test/pull/4#issuecomment-41654390  

Have you actually tried? I have no such issue, even with “-Weverything” (branches feature/quickbookdoc-fixcompilationissues / feature/quickbookdoc).  
  
So I would like to know:  
- which flavor of clang  
- where are the warnings  
  
Best,  
Raffi / raffi.enficiaud@free.fr  
  
On 29 Apr 2014, at 02:18, Noel Belcourt notifications@github.com wrote:  
  
> The issue is a bogus clang compiler warning about operator precedence so there's nothing to fix.  
>   
> —  
> Reply to this email directly or view it on GitHub.

---
