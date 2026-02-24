# #167 - [help] Some questions about this project. [Closed]

> Username: balusch  
> Created at: 2022-04-26 08:16:57 UTC  
> Updated at: 2022-04-27 02:46:16 UTC  
> Closed at: 2022-04-27 02:46:16 UTC  
> Url: https://github.com/boostorg/url/issues/167  

I'm looking for a modern and efficient URL library(parsing, encoding/decoding...) written in C++ and finally find this project. After reading the readme and some source files, I think it is indeed what I need! However, I also have some questions about it and wish you can help me with them.  
  
- The project name is **Boost.URL**, but it is said not an official sub-project of Boost yet. Will this project be merged into the Boost C++ library in the future?  
- Is the project ready to be used in the production evnrionment now, or still in early development?  
- My project is using Boost as a third-party depencency, and is there any way to build this project with such an external Boost library?  
- After skimming the CMakeLists.txt I found that it requires Boost-1.78.0, which is almost the newest version. Does it really need such a high version? And if not, which version of boost is the miminum requirement of Boost.URL?  
  
Thanks in advance.

---

## Comment 1

> Username: sehe  
> Created at: 2022-04-26 11:50:25 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1109699315  

(The review process for acceptance into Boost is underway, and can be followed on the Boost mailing list)

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-04-26 13:40:25 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1109812353  

Hi @balusch   
  
> The project name is Boost.URL, but it is said not an official sub-project of Boost yet. Will this project be merged into the Boost C++ library in the future?  
  
You can follow the review schedule here: https://www.boost.org/community/review_schedule.html  
  
> Is the project ready to be used in the production environment now, or still in early development?  
  
We test the library with the following compilers and methods:  
  
- https://master.url.cpp.al/url/overview.html#url.overview.supported_compilers  
- https://master.url.cpp.al/url/overview.html#url.overview.supported_compilers.quality_assurance  
  
This is the state of things:  
  
Branch          | [`master`](https://github.com/CPPAlliance/url/tree/master) | [`develop`](https://github.com/CPPAlliance/url/tree/develop) |  
--------------- | ----------------------------------------------------------- | ------------------------------------------------------------- |  
[Azure](https://azure.microsoft.com/en-us/services/devops/pipelines/) | [![Build Status](https://img.shields.io/azure-devops/build/vinniefalco/67375b5f-3402-44a3-a4ee-d5e856f94690/7/master)](https://dev.azure.com/vinniefalco/url/_build/latest?definitionId=7&branchName=master) | [![Build Status](https://img.shields.io/azure-devops/build/vinniefalco/67375b5f-3402-44a3-a4ee-d5e856f94690/7/develop)](https://dev.azure.com/vinniefalco/url/_build/latest?definitionId=7&branchName=develop)  
Docs            | [![Documentation](https://img.shields.io/badge/docs-master-brightgreen.svg)](http://master.url.cpp.al/) | [![Documentation](https://img.shields.io/badge/docs-develop-brightgreen.svg)](http://develop.url.cpp.al/)  
[Drone](https://drone.io/) | [![Build Status](https://drone.cpp.al/api/badges/CPPAlliance/url/status.svg?ref=refs/heads/master)](https://drone.cpp.al/CPPAlliance/url) | [![Build Status](https://drone.cpp.al/api/badges/CPPAlliance/url/status.svg?ref=refs/heads/develop)](https://drone.cpp.al/CPPAlliance/url)  
[GitHub Actions](https://github.com/) | [![CI](https://github.com/CPPAlliance/url/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/CPPAlliance/url/actions/workflows/ci.yml) | [![CI](https://github.com/CPPAlliance/url/actions/workflows/ci.yml/badge.svg?branch=develop)](https://github.com/CPPAlliance/url/actions/workflows/ci.yml)  
Matrix          | --- | ---   
Fuzzing         | --- | ---   
[Appveyor](https://ci.appveyor.com/) | [![Build status](https://ci.appveyor.com/api/projects/status/aewl3yuwn7skl5sr?svg=true&branch=master)](https://ci.appveyor.com/project/vinniefalco/cppalliance-url/branch/master) | [![Build status](https://ci.appveyor.com/api/projects/status/aewl3yuwn7skl5sr?svg=true&branch=develop)](https://ci.appveyor.com/project/vinniefalco/cppalliance-url/branch/develop)  
[codecov.io](https://codecov.io) | [![codecov](https://codecov.io/gh/CPPAlliance/url/branch/master/graph/badge.svg)](https://codecov.io/gh/CPPAlliance/url/branch/master) | [![codecov](https://codecov.io/gh/CPPAlliance/url/branch/develop/graph/badge.svg)](https://codecov.io/gh/CPPAlliance/url/branch/develop)  
  
  
  
> My project is using Boost as a third-party dependency, and is there any way to build this project with such an external Boost library?  
  
I'm not sure I understand the question. But yes, it depends on Boost, and you can build it with an external recent version of Boost. It has to be a recent version though.  
  
> After skimming the CMakeLists.txt I found that it requires Boost-1.78.0, which is almost the newest version. Does it really need such a high version? And if not, which version of boost is the minimum requirement of Boost.URL?  
  
As Boost.URL is a Boost proposal, the Boost.URL [master](https://github.com/CPPAlliance/url/tree/master) and [develop](https://github.com/CPPAlliance/url/tree/develop) branches depend on the most recent version of Boost in their [master](https://github.com/boostorg/boost/tree/master) and [develop](https://github.com/boostorg/boost/tree/develop) branches.   
  
In principle, the library depends on the Boost master/develop branches, and the most recent version is 1.79.0. In practice, 1.78.0 might be good enough because the library depends on `<boost/system/result.hpp>` for [`result<T>`](https://www.boost.org/doc/libs/1_78_0/libs/system/doc/html/system.html#usage_resultt_as_an_alternative_to_dual_apis), which was [included in Boost-1.78.0](https://www.boost.org/users/history/version_1_78_0.html). So Boost-1.77.0 definitely won't work.

---

## Comment 3

> Username: balusch  
> Created at: 2022-04-27 01:47:23 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1110442366  

@sehe Thanks! I'll make a subscription.

---

## Comment 4

> Username: balusch  
> Created at: 2022-04-27 01:56:33 UTC  
> Updated at: 2022-04-27 02:34:36 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1110446075  

Hi, @alandefreitas, thanks a lot, it really helps.  
  
> I'm not sure I understand the question. But yes, it depends on Boost, and you can build it with an external recent version of Boost. It has to be a recent version though.  
  
Yes, I just want to known how to build this project together with a Boost already used, or I should wait until it is merged into Boost and use the newest Boost then?  
  
And another concern is the compatibility with Boost of old version. I thought is would be compatible but that seems not to be the case according to your following statement. That is a sad thing. 😭

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-04-27 02:12:54 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1110454244  

> Yes, I just want to known how to build this project together with a Boost already used,   
  
If you have a *recent* version of Boost installed on your computer, you just need the headers from the `include` directory and then follow the instructions as usual.  
  
> or I should wait until it is merged into Boost and use the newest Boost then?  
  
That's also an option.  
  
> And another concern is the compatibility with Boost of old version.   
  
Yes. The library isn't even in Boost yet, so only recent versions of Boost.

---

## Comment 6

> Username: balusch  
> Created at: 2022-04-27 02:45:23 UTC  
> Url: https://github.com/boostorg/url/issues/167#issuecomment-1110473150  

> Yes. The library isn't even in Boost yet, so only recent versions of Boost.  
  
I see. Seems not suitable for our project which is using an older Boost and not easy to update, but I can try it in my new project.  
  
Anyway, thanks to your detailed explanation and such a good project -- URL utility is so fundamental and important in network/backend development.
