# #73 [CMake] Add minimal cmake file [Closed]

> Username: Mike-Devel  
> Created at: 2018-09-20 13:26:47 UTC  
> Updated at: 2018-12-18 17:34:39 UTC  
> Closed at: 2018-09-20 13:51:46 UTC  
> Url: https://github.com/boostorg/winapi/pull/73  

Generates cmake target that other libraries can use to express their dependency on this library and retrieve any configuration information such as the include directory, binary to link to (if any), transitive dependencies, necessary compiler options or the required c++ standards level.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-09-20 13:51:46 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423191075  

I'm not going to accept any CMake submissions unless they have passed review and have been accepted by Boost.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-09-20 14:24:38 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423203008  

Fair enough  
  
Just to clarify:  
  
- Do you mean I should let that specific PR be reviewed by the boost mailing list?   
- Or are you waiting for a more complete, library-backed solution to go through the boost library review process and then you would only accept a PRs that use cmake functions provided by that library?  
- Or are you waiting for a full fledged solution that enables cmake in all boost libraries at once to be reviewed on the boost ML and you are only going to accept PRs that are part of that solution?  
  
I'm actually not quite sure which of the latter two categories BCM (as an example) belongs to. It afaik has cmake files for every boost library, but I don't know if they will be part of the review or if they just serve as a POC/examples.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-09-20 14:43:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423210023  

I would like a more complete solution that supports building and testing libraries and is reviewed and accepted according to the [Boost review process](https://www.boost.org/community/reviews.html). Such a solution should be unified in the sense that it should be applicable to all Boost libraries and offer a unified interface to users for building, testing and consuming Boost libraries. Naturally, such a solution should also include documentation. To put it shortly, it should be an alternative to Boost.Build. Such a solution may or may not include a library, it's not essential. Although, given the amount of features, it likely will.  
  
The reason I'm not willing to accept partial solutions is that (a) it is against Boost review policy, (b) it discourages more complete solutions and makes their adoption more complicated, if at all possible, and (c) it adds burden on the maintainers while offering no gains to them.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-09-20 15:12:26 UTC  
> Updated_at: 2018-09-20 16:37:12 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423220849  

Thanks for the explanation.   
  
I don't really agree with(a): Besides the fact that I'm not proposing a new library here anyway, every library maintainer is - to the best of my knowledge - free to enable cmake in his library anyway (in addition to b2) and in fact, some have done so long before I started my efforts (yap, hana, beast, serialization, ...). I don't see how this PR is any different, but  of course I have a biased opinion about that ;)  
  
Also, one of the reasons my cmake file is so minimal (no tests, no docs, no installation etc) is precisely so that it doesn't compete with or derail the acceptance of a complete solution like BCM, but again it might not have the effect that I desire.  
  
However, I completely understand that you want to wait for a complete and reviewed solution that is used everywhere accross boost and don't want to support a half-baked one in addition to b2.  
  
Anyway, thanks for taking the time to respond.   
I hope Robert Ramey's proposal will gain enough support on the ML such that the formal review process can actuall start in the expected time frame.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-09-20 16:34:18 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423249687  

The problem with not accepting this PR is that it prevents others downstream from pursuing "half-fledged" solutions that nevertheless work for certain subset of use cases. No discouragement of full-fledged solutions would be effected by this minimal CMakeLists; on the contrary, it makes their appearance more likely, via a gradual approach.  
  
There's not much to maintain either; it's just the dependency list (config+predef) and it has been static for a while.  
  
Now porting the tests to CMake would indeed be much of a burden, but nobody expects that at the moment. The goal is merely to enable dependencies of Winapi to work by declaring the proper target.

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-09-20 19:39:52 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423307585  

> The problem with not accepting this PR is that it prevents others downstream from pursuing "half-fledged" solutions that nevertheless work for certain subset of use cases.  
  
I understand and still don't want to merge this. Merging it would mean committing to whatever interface the CMakeLists provides, which may be different from what we will have in the full solution. Maintaining this interface and dealing with deprecation is a problem.

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-09-20 20:00:53 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-423313698  

This minimalistic skeleton is something all solutions will have - a full solution will only add to it. The things that may change are the project naming convention (BoostFoo vs boost-foo vs boost_foo) or the target namespace (Boost::foo vs boost::foo) but the change should be relatively painless.  
  
And of course Winapi is not user-visible, so that's even less of an issue here.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2018-12-16 13:31:59 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-447643631  

As the efforts for a full-fledged solution have apparently stalled (yet again) I'd like to put this PR up for discussion once again.  
  
I understand the reluctance to add something that might become outdated soon(-ish) but experience shows that the top-down introduction if cmake is unlikely to happen all too soon. I deliberately kept the capabilities (and thus interface) to a minimum precisely so that it was easy to maintain backwards compatibility.  
  
The interface "contract" that this PR is introducing is roughly this:  
  
    add_subdirectory( ./libs/winapi )  
  
    Requries: available cmake targets: Boost::config and Boost::predef  
    Provides: cmake target Boost::winapi with assiociated interface include directory "./libs/winapi/include"  
  
Nothing more, nothing less. Even if you don't want to just say "this is experimenatal, don't depend on it", it should be easy to maintain this contract even if a different cmake solution is used boostwide at some point in the future.  
  
All that being said, there is one caveat: This PR is only usefull, after this one has been merged to boost predef: https://github.com/boostorg/predef/pull/86  
  
FYI: By last count, there are 14 libraries that use this schema so far.

---

## Comment 9

> Username: Lastique  
> Created_at: 2018-12-17 02:17:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-447702596  

> As the efforts for a full-fledged solution have apparently stalled (yet again)  
  
Have it? So far things are going according to Robert's schedule, AFAICT.  
  
I'm still standing by my opinion. This is true not only wrt. Boost.WinAPI, but also other libraries I maintain.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-12-17 08:27:17 UTC  
> Updated_at: 2018-12-17 08:27:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-447760554  

> Have it? So far things are going according to Robert's schedule, AFAICT.  
  
Wasn't the plan to post a call for submissions by start of November? The only draft that was posted was shot down on boost steering (https://groups.google.com/forum/m/#!topic/boost-steering/5ifzupxKINI) and I don't believe a new one was posted since then. I also asked on slack about it and Robert didn't sound as utf he is pursuing this any further.  
  
I would be very glad to hear that I am wrong though.  
  
> I'm still standing by my opinion. This is true not only wrt. Boost.WinAPI, but also other libraries I maintain.  
  
Just so I don't bother you further: Which ones do you maintain?

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-12-17 09:59:19 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-447786676  

I've posted a request to clarify the situation on the ML.  
  
> Which ones do you maintain?  
  
Boost.Log, Boost.Filesystem, Boost.Integer, Boost.Atomic, Boost.Sync, Boost.Core, Boost.WinAPI. See libs/maintainers.txt.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-12-18 11:15:49 UTC  
> Updated_at: 2018-12-18 11:16:41 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-448186272  

> I've posted a request to clarify the situation on the ML.  
  
Probably a good idea to put this on the ML.  
  
> >Which ones do you maintain?  
>  
> Boost.Log, Boost.Filesystem, Boost.Integer, Boost.Atomic, Boost.Sync, Boost.Core, Boost.WinAPI. See libs/maintainers.txt.  
  
I had a look but I a) didn't know if the list was up do date (e.g. I thought Beman Dawes retired) and b) wasn't sure how to treat repos with multiple maitnainers. E.g. Boost.Core already has such a cmake file despite you being on the list of maintainers (afaik it was added by Peter Dimov).

---

## Comment 13

> Username: Lastique  
> Created_at: 2018-12-18 14:27:25 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-448238450  

> I had a look but I a) didn't know if the list was up do date (e.g. I thought Beman Dawes retired)  
  
The list is generated from library metadata, so it is as actual as it can be. I left Beman as a maintainer because maybe he would still like to provide some input on the library evolution or patches.

---

## Comment 14

> Username: Lastique  
> Created_at: 2018-12-18 16:30:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-448282117  

Given that Robert's plan have been confirmed to have stalled, and we might not even have a review of CMake proposals, I suppose I have to bite the bullet and add a limited support for CMake, if only to allow downstream libraries to do it if they want to. See 4246c8987a7f469a31c795414433fbf5fa71bebc.  
  
It currently relies on Boost.Predef having a CMake file as well, which it does not.

---

## Comment 15

> Username: Mike-Devel  
> Created_at: 2018-12-18 17:34:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/73#issuecomment-448304408  

> It currently relies on Boost.Predef having a CMake file as well, which it does not.  
  
Yes, I mentioned that before. I'll try to get an update from Rene if he's planning to add it at all or not. If Boost.Predef doesn't get a cmake file, the Cmake file here doesn't make much sense in its current form.

---
