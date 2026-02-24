# #242 [CMake] Generate cmake target for boost config that other libraries can depend on [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-14 13:08:12 UTC  
> Updated at: 2018-10-08 08:38:45 UTC  
> Merged at: 2018-09-28 11:02:58 UTC  
> Closed at: 2018-09-28 11:02:58 UTC  
> Url: https://github.com/boostorg/config/pull/242  

For further reference: https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-09-20 09:50:02 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-423118444  

Would be nice if this (with an appropriate copyright banner added) could get merged, because without it, CMakeLists.txt in other libraries are unusable as they all refer to Boost::config.  
  
For instance, I've added, as an experiment, CMake-based testing to Assert, but it presently fails:  
  
https://travis-ci.org/boostorg/assert/jobs/430784534

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-09-25 13:21:23 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424340758  

John, thoughts, comments?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-09-25 16:49:54 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424418974  

Apologies, I'm way behind :(  
  
Question: lets say we merge this and folks folks start depending on it "because it's there", do we have an issue later if we adopt a different boost-wide solution later?

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-09-25 17:02:06 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424423157  

We also do not currently have any regression or CI way to test these CMake additions to show that they actually work in CMake based projects which use Boost. Boost policy has always been that there must be a way to verify that changes work correctly, not just that they look correct.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2018-09-25 17:12:33 UTC  
> Updated_at: 2018-09-25 18:16:49 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424426454  

@jzmaddock:  
> do we have an issue later if we adopt a different boost-wide solution later?  
  
Nothing that I can think of as there is (deliberately) so little functionality in it.  
   
One thing I could imagine is that whatever solution comes up later chooses a different naming scheme (e.g. `boost::config` vs `Boost::config`). it would be trivial to support both, so maintaining backwards compatibility would not be a problem, but of course it is not an ideal situation.

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2018-09-25 17:48:26 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424438195  

@eldiener: I can absolutely add a test that gets run by travis. I probably don't know enough about the rest of the boost test infrastructure to integrate it there.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2018-09-25 18:01:18 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424442737  

Looking at the travis file, the main difficulty is actually to isolate the cmake treaty against the b2 stuff and vice versa, to make sure it really works without it.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-09-25 18:11:05 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424446076  

It's possible to work on testing via CMake (one way or another), but without Config, nothing can proceed.  
  
https://github.com/boostorg/assert/blob/develop/CMakeLists.txt has support for testing and https://github.com/boostorg/assert/blob/feature/cmake-test/.travis.yml#L37 uses it, but it fails without Config having CMakeLists.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-09-25 18:39:06 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424455164  

It's better to create a separate Travis job with its own install/script entries instead of attaching the CMake tests everywhere.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-09-25 18:39:38 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424455342  

OK, I added a test for the cmake script. As I can't test the travis script localy it might require a few iterations.

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-09-25 18:40:44 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424455713  

Attaching the Travis changes to this PR lowers its chances of being accepted IMO.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-09-25 18:45:13 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424457176  

@pdimov: Thats one of the reasons I didn't do it originally. Much more things to bikeshed about ;). If jzmaddock doesn't like it, I have no problem with removing it again, but at least people can see that "it works".

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-09-25 18:53:06 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424459646  

At least do it right then.  
  
```  
- os: linux  
  install: true  
  script:  
    - mkdir __build__ && cd __build__  
    - cmake ../test/cmake  
    - cmake --build .  
```  
  
:-)

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-09-25 18:56:54 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424460897  

Either way, I think that Config is not the right place to experiment with testing CMake files. We'll do that elsewhere and only add it here when it's ready. We just need the basic CMakeLists here because Config is a dependency of everything and the elsewhere work can't proceed.

---

## Comment 15

> Username: Mike-Devel  
> Created_at: 2018-09-25 20:48:41 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-424496291  

Fair enough.  I'll only be able to revert this tomorrow though.

---

## Comment 16

> Username: glenfe  
> Created_at: 2018-09-27 11:23:06 UTC  
> Updated_at: 2018-09-27 11:23:30 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425054598  

What happened to reverting this tomorrow (or yesterday)?

---

## Comment 17

> Username: Mike-Devel  
> Created_at: 2018-09-27 17:30:42 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425177346  

Sorry for the delay - life happend

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2018-09-28 11:03:21 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425401413  

Still not sure we're doing the right thing... but now merged.  Enjoy!

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2018-09-28 11:10:20 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425402769  

I've pushed some comments to the CMake files to the effect that these are experimental.  
  
Some remaining issues:  
  
* I think that test/cmake/* should all go, in particular it contains `project(BoostFilesystem_test)` that looks like a flat out bug?  
* If we're adding tests, please add a cmake file directly to /test/ that builds config_info.cpp and config_test.cpp as separate tests.

---

## Comment 20

> Username: glenfe  
> Created_at: 2018-09-28 11:40:10 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425409046  

> I think that test/cmake/* should all go  
  
Yes. The only thing needed here is the one CMakeLists.txt. Feel free to nuke whatever that test/cmake is.

---

## Comment 21

> Username: Mike-Devel  
> Created_at: 2018-09-28 11:44:19 UTC  
> Updated_at: 2018-09-28 11:46:10 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425409929  

@jzmaddock: Thanks   
  
> I think that test/cmake/* should all go, in particular it contains project(BoostFilesystem_test) that looks like a flat out bug?  
  
That folder contains the testproject for the `CMakeLists.txt` file requrested by @eldiener (i.e. can the cmake file used by another cmake based project). So it's purpose is  not to run the Boost.config tests but to test the cmake file itself.   
And yes, the `project(BoostFilesystem_test)` is a typo (although an inconsequential one) and should have been `project(BoostConfig_test)`.I originally had that test run by travis-CI, but removed it as requested by @pdimov. It can still be used for manual testing or hooked up with travis or the general boost.config test.  
  
If you don't think such a test is necessary, then just remove that folder.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2018-09-28 11:48:28 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425410823  

> Feel free to nuke whatever that test/cmake is.  
  
Nuclear option exercised :)  
  
If someone wants to submit a CMake testing script that's fine, but it should actually build and run the existing tests...

---

## Comment 23

> Username: Mike-Devel  
> Created_at: 2018-09-28 11:51:16 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425411435  

> If someone wants to submit a CMake testing script that's fine, but it should actually build and run the existing tests...  
  
Again, that was not the purpose of the script. I just did what @eldiener requested

---

## Comment 24

> Username: Mike-Devel  
> Created_at: 2018-09-28 11:57:05 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425412663  

Btw.: Does anyone have an opinion on the proposed top level cmake file (https://github.com/boostorg/boost/pull/193)?

---

## Comment 25

> Username: eldiener  
> Created_at: 2018-09-28 19:20:57 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425539913  

The only thing I have requested, and it appears to have been possibly misunderstood, is that if a CMake PR is done for a particular library, then that specific library should have a means to test that the CMake PR is correct and does what it is supposedf to be doing, which I gather is to make the particular library usable by end-user CMake scripts. I have not requested that CMake be used in place of b2 to run tests in a particular library's test directory, if that is what is actually believed. I believe that a CMake PR should be treated like any other PR; there should be a way to test that it is correct for the particular library, or else what is the point of the PR.

---

## Comment 26

> Username: pdimov  
> Created_at: 2018-09-28 19:39:10 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425544287  

That was actually what the addition did (tried to do) - it added a mock user project in `test/cmake` (which could probably have been better named as `test/cmake_test_project`) and then built it. The Travis addition was however wrong.

---

## Comment 27

> Username: eldiener  
> Created_at: 2018-09-28 19:49:37 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425546753  

The 'test/cmake' is a directory in each particular library ? How would the preprocessor library ( or any library ) verify that the proposed CMake PR for that library is correct ? The usual way of doing this is to add a test to the bjam file in the library's test subdirectory which verifies that the PR is valid, if some test does not already exist which tests the validity of the PR. Is it not possible to do this with the CMake PRs being proposed ?

---

## Comment 28

> Username: pdimov  
> Created_at: 2018-09-28 19:57:57 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425548939  

If you want `cmake_test.cpp` in the `test` directory, which is then executed by `b2 test`, to test the CMake file, that's going to be a bit more convoluted because the functionality isn't in C++. The test will basically need to execute the `cmake` commands.  
  
I see that Mike has posted a question to that effect on the list.  
  
It's easier to test the sequence of `cmake` commands in a Travis job instead of from a C++ test program, but the other way is I suppose possible too.  
  
I've re-added the CMake test project to a feature branch here: https://github.com/boostorg/config/commit/bef8ef7b80814d58680522dd898c0aeffbef819e  
  
We'll need to wait on Travis a bit to see if I managed to get it right.

---

## Comment 29

> Username: pdimov  
> Created_at: 2018-09-28 21:28:03 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425571144  

Yes I did: https://travis-ci.org/boostorg/config/jobs/434773579

---

## Comment 30

> Username: Mike-Devel  
> Created_at: 2018-09-29 12:42:51 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425642176  

@eldiener:  
> , then that specific library should have a means to test that the CMake PR is correct and does what it is supposedf to be doing, which I gather is to make the particular library usable by end-user CMake scripts.  
  
Which is exactly what that (first nuked and now fixed & resurected) code did.  
  
> I have not requested that CMake be used in place of b2 to run tests in a particular library's test directory, if that is what is actually believed.  
  
No worries, I never understood it that way, nor did I ever had the plan to provide cmake files that run the regular library unit tests.  
  
@pdimov:  
> I've re-added the CMake test project to a feature branch here: bef8ef7  
  
Thanks. Maybe you should also a job for Mac? IT is not really relevant here  
  
@glenfe:  
> Feel free to nuke whatever that test/cmake is.  
  
Sorry if that sounds childish, but do you always recommend to delete other people's code whose purpose you don't understand? I mean, just like you I'm only interested in the top level CMakeLists.txt file, so I don't really care what happens to those particular files, but generally that seems not like an ideal approach.

---

## Comment 31

> Username: glenfe  
> Created_at: 2018-09-29 12:54:50 UTC  
> Updated_at: 2018-09-29 13:38:50 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425643104  

> but do you always recommend to delete other people's code  
  
Not always. In circumstances where the particulars are a facsimile of the ones in this circumstance, always.  
  
> I mean, just like you I'm only interested in the top level CMakeLists.txt file, so I don't really care what happens to those particular files  
  
Excellent. Then they won't be missed by either of us  
.  
> generally that seems not like an ideal approach.  
  
Generally, I feel it is entirely ideal that a library maintainer removes something you attempted to do that doesn't work yet or isn't fit for purpose yet.

---

## Comment 32

> Username: glenfe  
> Created_at: 2018-09-29 13:09:31 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-425644132  

To be clear, the maintainer of the library here was rightly concerned by whatever you had attempted to do in test/cmake because it:  
  
* Looked clearly wrong (should not have even involved Boost.Filesystem)  
* More importantly, did not even work (as indicated by the Travis results)  
  
That he raised concerns about the above, that he inquired about the removal of those files, that he was encouraged to remove them, that he did remove them with the commit message "Remove malformed CMake testing files", or that he subsequently posted "If someone wants to submit a CMake testing script that's fine, but it should actually build and run the existing tests..." shouldn't surprise anyone.

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2018-10-05 18:40:44 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-427461774  

Before I start adding more stuff from other PR's, just a quick question about naming conventions - be prepared for a full bike shed discussion, and no this probably isn't the right forum...but - this adds 2 named targets for the library - "boost_config" and "Boost::config".  Now, normally we tend to use "Boost.LibraryName" when referring to a particular library, and if we're using a "::" separator, then we should follow the namespace name, so all lowercase "boost::library".  Thoughts?

---

## Comment 34

> Username: pdimov  
> Created_at: 2018-10-05 18:46:20 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-427463427  

We can go either way on that one and I originally did use `boost::library`. However, capital case for the target namespace is the CMake convention, and the existing FindBoost.cmake naming does use f.ex. `Boost::filesystem` for the targets. So... `Boost::` it is.

---

## Comment 35

> Username: Mike-Devel  
> Created_at: 2018-10-08 08:38:45 UTC  
> Url: https://github.com/boostorg/config/pull/242#issuecomment-427758516  

A bit late but anyhow:   
- `boost_config should be considered a private variable name. No one should reference it and every library maintainer may change the name at their leisure . Unfortunately, I don't thinkg cmake provides an easy way to make targets only referencable locally (Niall Dougles mentioned one way on the boost ML).   
  
- `Boost::config` - as explained by pdimov - follows the naming convention set by cmake itself (e.g. if cmake can find a system installed Boost.Filesystem library it will provide a `Boost::filesystem` target that can be imported via `find_package(Boost COMPONENTS filesystem)` (https://cmake.org/cmake/help/v3.13/module/FindBoost.html).   
Obviously there are advantages and disadvantages to this: Danger of collision / mixup vs consistency. One reason I didn't consider the collison a dealbreaker is that :  
  a) As far as I can tell, cmake will always prefer the local target, which is what we want  
  b) Even if the boost targets are named differently to prevent collisions on the cmake level, your compiler would still find the system installed boost headers and libraries anyway, so I don't know a reliable way to prevent mixups anyway (which is one reason I don't like system package managers used for dependency resolution during c++ development).

---
