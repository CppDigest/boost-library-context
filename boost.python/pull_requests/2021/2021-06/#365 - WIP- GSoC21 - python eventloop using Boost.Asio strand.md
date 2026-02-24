# #365 WIP: GSoC21 - python eventloop using Boost.Asio strand [Open]

> Username: philoinovsky  
> Created at: 2021-06-08 15:02:36 UTC  
> Updated at: 2024-12-31 16:47:24 UTC  
> Url: https://github.com/boostorg/python/pull/365  

Proposal: https://github.com/philoinovsky/GSoC21/blob/main/5126362551025664_1617947181_Pan_Yue_GSoC21_Proposal.pdf

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2021-06-08 15:09:28 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-856855260  

Do you have some document where you describe the design behind this change ? That would help me review the code.

---

## Comment 2

> Username: philoinovsky  
> Created_at: 2021-06-08 15:14:58 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-856859934  

@stefanseefeld Sure, this is my gsoc proposal! https://github.com/philoinovsky/GSoC21/blob/main/5126362551025664_1617947181_Pan_Yue_GSoC21_Proposal.pdf

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2021-06-08 17:55:47 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-856974577  

Thanks, that document provides a high-level overview of what the goal of your project is. What I'm looking for is something that summarizes the goal of this PR. I realize your PR is marked as Work In Progress, so it might in fact not be meant to be merged, or even reviewed. (You comment out a bunch of lines, which of course isn't acceptable for a real PR.)  
Just trying to understand the intent of this submission.

---

## Comment 4

> Username: vinipsmaker  
> Created_at: 2021-06-12 14:31:14 UTC  
> Updated_at: 2021-06-12 14:32:10 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860061373  

Hi @stefanseefeld,  
  
could you enable CI tests on PRs so Pan can use the Boost infra to test this work?  
  
This is a WIP and not yet ready to merge, but CI could help on the development. Hence the PR.  
  
I'll be mentoring Pan and reviewing everything that pertains to Boost.Asio usage so you can assume this part will be covered by the time the WIP title/label is removed from the PR. Also we'll be pinging you periodically when certain design decisions show up as to make sure little friction appears when the PR is ready for merge. In the meantime there's little reason to invest time studying this early work (unless you want to).

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2021-06-12 20:25:31 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860104543  

Hi Vinicius,  
  
On 2021-06-12 10:31 a.m., Vinícius dos Santos Oliveira wrote:  
>  
> Hi @stefanseefeld <https://github.com/stefanseefeld>,  
>  
> could you enable CI tests on PRs so Pan can use the Boost infra to   
> test this work?  
>  
> This is a WIP and not yet ready to merge, but CI could help on the   
> development. Hence the PR.  
>  
> I'll be mentoring Pan and reviewing everything that pertains to   
> Boost.Asio usage so you can assume this part will be covered by the   
> time the WIP title is removed from the PR. Also we'll be pinging you   
> periodically when certain design decisions show up as to make sure   
> little friction appears when the PR is ready for merge. In the   
> meantime there's little reason to invest time studying this early work   
> (unless you want to).  
>  
I have to admit that I'm a bit surprised that you are working on a   
project that you expect ultimately to be merged into the Boost.Python   
repo without involving me right from the start of the project. Don't you   
think this would have been appropriate ?  
  
Anyhow, yes, I appreciate you including me in design review discussions.  
  
Meanwhile, I have approved workflow / CI checks, so you should see the   
first test results soon.  
  
Stefan  
--   
  
       ...ich hab' noch einen Koffer in Berlin...

---

## Comment 6

> Username: vinipsmaker  
> Created_at: 2021-06-12 20:56:32 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860107592  

> Don't you think this would have been appropriate ?  
  
Yes. My bad.  
  
> without involving me right from the start of the project  
  
So, what we're looking at here for the end goal is a simple way to enable Boost.Asio-based programs to reuse Python 3's asyncio-based libs on the same event loop. Python has a huge ecosystem (e.g. web crawlers) and it can help to bootstrap new C++ projects.  
  
Asyncio is only a Python 3 thing, so Python 2 is out of the question. Also the scenario is a custom C++ program acting as the Python host so you have all the consequences that follow from that (e.g. do not generate an external C/C++ lib to be imported in a Python program). I have already looked at asyncio's [event loop](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio-event-loop) and I have a good idea on how it can be integrated on Boost.Asio. I'm sure Pan is a capable programmer for this task.  
  
And another thing is the ability to use multiple Python interpreters in the same C++ host in the same vein as [subinterpreters for Python](https://lwn.net/Articles/820424/). This feature would be a perfect fit for Boost.Asio's strands. It's not a magical solution and it won't work against every Python library, but that's expected. If the user wants to use multiple Python VMs in his program, that's on him (he'll just have to exclude dangerous Python libs from the pool of choices).  
  
Again, the end goal is just an easy on/off switch that we can enable to magically setup a Python VM to use the implementation of our own asyncio implementation that defers all work to Boost.Asio. So ideally there shouldn't be many API additions. Ideally it should be just one function call that we expose in Boost.Python (and it can only be used against Python 3 obviously).

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2021-06-12 21:06:23 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860108680  

Thanks for the intro. Yes, I know Python's `asyncio` module, and in fact I'm using it heavily in my own `faber` tool (the one you are using to build this project right now :-) )  
I understand the appeal to use `Boost.Asio` as backend for Python's async / await logic. In fact, at some point it may even be possible to use this as backend for C++'s own runtime support for C++20 coroutines.  
  
As far as `Boost.Python` integration is concerned, it seems to me this should be a separate library, as it's orthogonal to language bindings. If I understand correctly, the new functionality you are proposing can be used as drop-in replacement for Python's own event loop (independent of whether C++ extension modules are being used), and likewise, normal `Boost.Python` users don't have to know what event loop implementation the Python runtime is using to power async / await support. Am I understanding correctly ?

---

## Comment 8

> Username: vinipsmaker  
> Created_at: 2021-06-12 22:12:39 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860119875  

> As far as `Boost.Python` integration is concerned, it seems to me this should be a separate library, as it's orthogonal to language bindings. If I understand correctly, the new functionality you are proposing can be used as drop-in replacement for Python's own event loop (independent of whether C++ extension modules are being used), and likewise, normal `Boost.Python` users don't have to know what event loop implementation the Python runtime is using to power async / await support. Am I understanding correctly ?  
  
Both approaches are valid, but it's ultimately Boost.Python's maintainer that has a say whether the new module should be a subdirectory under Boost.Python or a separate library entirely.  
  
What's your say here? I'll trust your judgment no matter the answer.  
  
In case you think a separate library would be more appropriate, would you be willing to act as a review manager when this library reaches the stage where it can be considered mature? I think it'll help tons judging by my experience when I last gauged interest on a new library that made use of pre-existing Boost libraries.  
  
Mailing list members may not even look at the code if it has the same domain as a pre-existing library already in Boost. And yes, they'll just suggest to look for integration on the pre-existing library as they trust Boost maintainers that endured the review process already.  
  
> If I understand correctly, the new functionality you are proposing can be used as drop-in replacement for Python's own event loop  
  
That's correct.  
  
It has one small restriction, but that's minor and expected: If Python is a guest on a foreign event loop, then Python code shouldn't call `run_*()`. This function will throw an exception if called from Python as it's really the C++ host that will be calling the implementation for this function.  
  
However this restriction shouldn't affect any library as libraries don't call `run_*()`.  
  
> (independent of whether C++ extension modules are being used)  
  
Correct.  
  
Therefore I at least tend to agree that it's orthogonal to Boost.Python.  
  
> and likewise, normal `Boost.Python` users don't have to know what event loop implementation the Python runtime is using to power async / await support  
  
Also correct.

---

## Comment 9

> Username: stefanseefeld  
> Created_at: 2021-06-12 22:20:39 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860120562  

On 2021-06-12 6:12 p.m., Vinícius dos Santos Oliveira wrote:  
> Both approaches are valid, but it's ultimately Boost.Python's   
> maintainer that has a say whether the new module should be a   
> subdirectory under Boost.Python or a separate library entirely.  
>  
> What's your say here? I'll trust your judgment no matter the answer.  
>  
For avoidance of doubt: by "separate library" I didn't mean "separate   
project". `Boost.Python` right now already contains two libraries:   
`boost_python` and `boost_numpy`. Users who don't need `NumPy` support   
only need to use the former.  
  
Similarly, I can imagine the new event loop backend to be compiled into   
a distinct library (still part of `Boost.Python` !), so users may only   
link to the libraries they actually need.  
  
I can still imagine good reasons to keep the new functionality in   
`Boost.Python`, for example if some C++ extension modules make direct   
calls into the new API. We'll see whether that makes sense as the   
project matures and we gather experience prototyping with it.

---

## Comment 10

> Username: vinipsmaker  
> Created_at: 2021-06-12 22:27:44 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-860121055  

> Similarly, I can imagine the new event loop backend to be compiled into  
> a distinct library (still part of `Boost.Python` !), so users may only  
> link to the libraries they actually need.  
  
Okay, let's roll with that for now then.  
  
I'm not sure there'll be a library to link to though. Boost.Asio's flexibility really demands a header-only library for most of the time. At least strand will be a concept that can have multiple implementations (sometimes user-provided).

---

## Comment 11

> Username: vinipsmaker  
> Created_at: 2021-07-07 19:42:15 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-875880682  

@stefanseefeld would it be okay to have a requirement for C++14 or later on this sublibrary? The integration code with Boost.Asio is already becoming too spaghetty for my taste and proper lambda expressions + move semantics would help tons on the implementation. Other Boost.Asio-based libraries usually have higher requirements anyway (e.g. Boost.Beast requires C++11), so we won't be really removing a significant audience here.

---

## Comment 12

> Username: stefanseefeld  
> Created_at: 2021-07-07 20:50:27 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-875923475  

@vinipsmaker yes, that's definitely fine. (One more reason to have this new functionality in its own library, as that makes it easier to constrain a new library than an existing one !)

---

## Comment 13

> Username: vinipsmaker  
> Created_at: 2021-07-18 16:30:40 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-882082831  

@stefanseefeld, we've been using Boost.Python's own abstractions to the C API where possible so far. However we'll be migrating to `Py_NewInterpreter()` calls soon (each subinterpreter will be protected by its own [Boost.Asio strand](https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/overview/core/strands.html)) and I have a hunch that we'll be forced to migrate to the low-level C API.  
  
So that's just a heads-up. We might migrate to the low-level C API _internally_ in future interactions of the code.

---

## Comment 14

> Username: stefanseefeld  
> Created_at: 2021-09-16 19:33:57 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-921188758  

hi @vinipsmaker , I hear the GSoC 2021 projects have completed successfully. What is the state of this PR ? Are you planning to prepare it for review / merge ?

---

## Comment 15

> Username: vinipsmaker  
> Created_at: 2021-09-16 22:54:51 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-921316877  

hi @stefanseefeld  
  
> What is the state of this PR?  
  
Well, most of the GSoC was spent on research, so it was successful. As for the implementation, it's not ready.  
  
First of all, Python subinterpreters still share the same GIL, so the most exciting idea had to be abandoned. Supposedly this issue would be fixed by Python 3.10, but only time will tell. The implementation just abandoned the idea of subinterpreters and gone back to plain single global interpreter.  
  
There are still concurrency issues to fix, and lots of features to add before the PR is useful. I wanted to keep mentoring this work after GSoC ended, but I'm very busy and I won't have the time this year. I hope I can mentor this project again next year and then we'll see.  
  
@philoinovsky also learned a lot during the project, so maybe you'll want to hear one word or two from him.

---

## Comment 16

> Username: trim21  
> Created_at: 2024-12-31 11:15:58 UTC  
> Updated_at: 2024-12-31 11:16:49 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-2566359866  

do you mind I use some of your code as a pypi package？

---

## Comment 17

> Username: stefanseefeld  
> Created_at: 2024-12-31 16:39:53 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-2566581790  

as a pypi package it should be backed by support from a project (where users can file bugs, for example). So unless you want to fork this repo and then maintain that yourself, I highly recommend that you contribute anything that's necessary to release this work to *this* project, so we can then publish it from here.

---

## Comment 18

> Username: trim21  
> Created_at: 2024-12-31 16:44:47 UTC  
> Updated_at: 2024-12-31 16:47:24 UTC  
> Url: https://github.com/boostorg/python/pull/365#issuecomment-2566584568  

> as a pypi package it should be backed by support from a project (where users can file bugs, for example). So unless you want to fork this repo and then maintain that yourself, I highly recommend that you contribute anything that's necessary to release this work to _this_ project, so we can then publish it from here.  
  
Yes, that's what I intent to do. I do not want to fork this whole project, but I do want to cherry pick this PR as a project.  
  
I'd like to try to use boost asio as a asyncio event loop policy like uvloop, but there is no need to use boost.python.

---
