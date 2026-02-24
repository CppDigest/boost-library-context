# #45 - [Q] Future of Boost.Nowide [Closed]

> Username: Flamefire  
> Created at: 2019-11-08 17:24:48 UTC  
> Updated at: 2019-12-13 18:25:21 UTC  
> Closed at: 2019-12-13 18:25:21 UTC  
> Url: https://github.com/boostorg/nowide/issues/45  

I'm a user of this library and already opened a few PRs. I've just seen that this was accepted into Boost 2 years ago. But nothing happened since.  
  
@artyom-beilis What is the intended future for this project? I'd really love for it to be either included into boost or refactored into a standalone library with boost integration/dependency.  
  
Reason is: This is super useful. It has even been included already in several linux distros, see e.g. https://bugzilla.redhat.com/show_bug.cgi?id=1502584. However there are some issues with some of them even being already fixed by open PRs. It would be great to have at least the community to enhance this.  
  
I'd suggest (if Boost inclusion is out for now) to create a simple library, which requires boost and has proper CMake files for building as submodule or standalone. My PR #27 is already used by RedHat repos, so that might be a good starting point. This combined with some travis CI to test PRs keeps the effort low.

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-11-26 16:59:31 UTC  
> Url: https://github.com/boostorg/nowide/issues/45#issuecomment-558723752  

I created a master branch in my fork incorporating the reviewfixes branch (https://github.com/artyom-beilis/nowide/commit/6bc55c95d2aa08f37fed3cc952619269812f4046) from here as well as all my PRs and various other fixes for failures detected during the setup of CI.  
  
It contains extensive CI that also verifies the standalone version as well as improved CMake scripts that install proper targets. Additionally the CMake Library on Linux is now an INTERFACE library meaning no linking (of an empty lib) is required. Releases on tags are also created automatically.  
  
See https://github.com/Flamefire/nowide  
  
@artyom-beilis Are you still interested in that library to maintain it and/or include it into boost? The master branch should be almost ready for it as I fixed all remaining issues from the review summary. Only thing missing is the Jamfile for the docu.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2019-11-30 13:50:30 UTC  
> Url: https://github.com/boostorg/nowide/issues/45#issuecomment-559970233  

What branch have you forked?   
  
This is the relevant one: https://github.com/artyom-beilis/nowide/tree/reviewfixes  
  
I still need to finish some review work (I have some open issues) unfortunatelly I still can't get my hands on it...

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-11-30 15:03:57 UTC  
> Url: https://github.com/boostorg/nowide/issues/45#issuecomment-559979473  

I started with your master and merged all my open PRs, then merged your reviewfixes. On top of that I started implementing CI, fix the remaining review issues and fix bugs detected. Basically all the review comments have been addressed now and some previously undetected bugs have been squashed.  
  
My focus was to but as much as possible on CI to verify future changes for standalone and boost version (the former is/was broken)  
  
However I'm currently working on the `filebuf` which has some inconsistencies and bugs, some of them only degraded performance by doing more than required by the standard. So I'm going through each function and check and fix them according to the standard. To be sure I templated the test cases to run them with the `std` and the `nowide` classes to detect non-bugs.  
  
Due to the current state I'd suggest to close my open PRs and create a new, big one with all my changes and have you review them in case I missed anything. Ping me when you got time for such thing or suggest an other approach.

---

## Comment 4

> Username: Flamefire  
> Created at: 2019-12-03 17:05:02 UTC  
> Url: https://github.com/boostorg/nowide/issues/45#issuecomment-561262640  

@pdimov if you need help, ping me  
  
To ease taking code from my fork, I'd suggest you merge the reviewfixes branch asap to avoid conflicts and apply clang format (with my style file, designed to make it consistent w/o changing more than required) I think afterwards we can easily diff files and you don't need to fix issues I already fixed. If you want I can then also try to extract small PRs, but the format needs to be adjusted first
