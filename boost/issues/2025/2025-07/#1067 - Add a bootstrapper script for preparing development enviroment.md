# #1067 - Add a bootstrapper script for preparing development enviroment [Open]

> Username: HO-COOH  
> Created at: 2025-07-19 16:09:28 UTC  
> Updated at: 2025-09-05 13:05:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1067  

Hi, I am a newcomer wanting to contribute to one of the boost library. I read through [the official getting started tutorial]() carefully, and found it unnecessarily complicated for anyone wanting to contribute.  
  
Some key points are:  
- C++ developers are expected to able to use CMake, but b2 is honestly speaking not very mainstream. (Actually I have never heard of it) And instead of having an official download link it requires manually go to the github repro and download the latest release. The is not most windows users are used to. This could be better automated by a bootstrapper script.  
- If I am interested in a specific library, I need to try to build it first, found out the missing dependencies, install those (and they themselves could have other libraries as dependencies). This could be tedious without a boostraper.  
- The documentation pages also need additional tooling to work.  
  
I image all these could be solved by adding something like a TUI and let new contributors (like me) to select the workflow they wanted. Powershell script would be a perfect choice as it is cross-platform and light to install. (Built-in on windows, one-liner to install on linux/mac os). If you think this is a good idea, I would like work on it.

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-09-04 03:55:03 UTC  
> Url: https://github.com/boostorg/boost/issues/1067#issuecomment-3251728527  

A couple of comments.  
  
cmake works for me, is there something there that could use improvement or documentation clarification?  
  
Linux people don't tend to be powershell literate, to be honest.

---

## Comment 2

> Username: HO-COOH  
> Created at: 2025-09-05 12:03:32 UTC  
> Updated at: 2025-09-05 12:03:49 UTC  
> Url: https://github.com/boostorg/boost/issues/1067#issuecomment-3258107147  

@nigels-com I am talking more about contributing to boost, that is maintaining some existing library (or create a new one). Currently  the workflow of using cmake is still under construction, and I figured out how to actually do that with pain:  
- I need to have the super project  
- define `-DBOOST_INCLUDE_LIBRARIES` in cmake arguments (and the way to achieve that will be different for different ides and editors to make debugging actually works)  
- define `DBUILD_TESTING` because you really should  
  
Creating a new library will require more bootstrapping of course. And the doc is not clear about it.  
  
And another thing is getting `Antora` to generate documentation pages, for contributing to docs.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-09-05 13:05:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1067#issuecomment-3258283921  

I had a fresh look at [Getting Started](https://www.boost.org/doc/user-guide/getting-started.html) again, and I really do think there is a structural problem there.  To me the _point_ of supporting cmake is to not require `b2`, at least to me.  (I respect that b2 has been getting the job done, day-in, day-out.). But in the starting guide we're constant steering people to `b2` because it's _recommended_ or because cmake isn't _necessary_.  
  
One thing I stumbled on in recent years is that besides the *official* release tarball, there is also a cmake-ready one that doesn't seem to be signposted.  Such as [boost-1.89.0-cmake.tar.gz](https://github.com/boostorg/boost/releases/download/boost-1.89.0/boost-1.89.0-cmake.tar.gz) which I've used to build boost binaries for downstream consumption, entirely via cmake.  
  
So through that lens I think cmake should be treated as more of a first-class citizen and option.  And I believe that is also helpful for boost adoption, broadly.  And having some sort of boost-specific UI as a _third_ option sort of a distraction.  
  
I use boost a fair bit, but `b2` hasn't seemed necessary, it's just another cmake thing, for the most part.
