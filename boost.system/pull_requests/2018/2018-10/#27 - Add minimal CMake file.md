# #27 [CMake] Add minimal CMake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-10-09 09:39:40 UTC  
> Updated at: 2019-01-04 17:49:14 UTC  
> Merged at: 2019-01-04 17:38:25 UTC  
> Closed at: 2019-01-04 17:38:25 UTC  
> Url: https://github.com/boostorg/system/pull/27  

I'm not quite sure, what to do with the winapi dependency. It doesn't seem as if Boost.WinApi will gain a cmake file anytime soon (https://github.com/boostorg/winapi/pull/73), so this PR will currently only work under linux.   
  
I see multiple possible workarounds:  
1. Don't add cmake support for boost.system at all until Boost::winapi is available (if ever)   
2. Only enable cmake on posix systems  
3. Ignore the dependency on winapi on the cmake level and let users that actually include the respective headers deal with it  
4. Add a dumy Boost::winapi target  
5. Let the user that calls this CMakeList.txt file provide a `Boost::winapi` target  
6. Add the winapi include directory to the target interface include directories  
7.Check for the availability of Boost::winapi target and if it is not, create a differently named dummy   
  
- Workarounds 1 and 2 are imho the most undesireable solutions due to the ripple effect  
- 3 is not very user friendly but might work in many situations, where the winapi header files are anyway in the system lookup path but on the other hand, this can lead to subtle errors if there is a version missmatch.  
- Number 4 has the problem that it will likely collide with other dumy targets and we have to guess the location of the winapi headers (most likely ../winapi/include).  
- 5 is again not too user friendly but on the other hand it is the most flexible solution, as the user probably knows best where to get the winapi headers from and if he needs them at all  
- 6 Seems the simplest workaround to me with the only drawback that we still have to guess the include path  
- 7 Is a bit more robust than 6, as it picks up the winapi target if it is provided (e.g. by the user or a modified version of the winapi library itself) but also more ugly

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-09 14:14:31 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428208771  

cc @Lastique -)

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-09 14:17:14 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428209833  

None of those look particularly appealing. If we assume a superproject, we can make it generate the targets of the uncooperative libraries, but for the no-superproject case, it's 4 or 6, both bad.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-10-09 14:35:06 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428216929  

Given that we only ship monolithic Boost, I don't see the point in these targets since the only include path you'll ever need is `$BOOST_ROOT`. I don't think our distribution model will change soon, most certainly not before we do the upcoming CMake proposals review. And I really hope that review ends with some sort of a solution.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-09 14:46:39 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428221574  

The intent here is to be able to use parts of Boost as submodules in your project, not from a release.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2018-10-09 16:14:13 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428255072  

> And I really hope that review ends with some sort of a solution.  
  
Has there been support for the process proposed by Robert? The mailing list seems relatively dead on the topic, but I've seen some comments about it on slack and maybe silince just means that most people are fine with it.  
  
I fear that at the end of the review process Robert will announce that proposal X has won and .... nothing will happen.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-09 17:47:33 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428285996  

> The intent here is to be able to use parts of Boost as submodules in your project, not from a release.  
  
As an example, see https://github.com/boostorg/mp11/tree/develop/test/cmake_subdir_test, and correspondingly https://github.com/boostorg/mp11/blob/develop/.travis.yml#L234. The idea is that the user project adds f.ex. mp11 as a submodule in `ext/mp11` and then does `add_subdirectory(ext/mp11)` and then `target_link_libraries(Boost::mp11)` as appropriate.  
  
For using `system`, one would add `ext/config`, `ext/system`, `ext/winapi`, `add_subdirectory` them, then link to `Boost::system`.

---

## Comment 7

> Username: Lastique  
> Created_at: 2018-10-09 19:14:49 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428315826  

@Mike-Devel Well, given that CMake always generates active discussions on the ML, I would say there are enough interested parties. Certainly, there are undermaintained libraries, which someone will have to update to support CMake, but I'm optimistic about it. For example, you took time to create PRs like this one, why not create a few similar PRs using an accepted solution? ;)  
  
@pdimov How would one obtain these libraries? From library git repo? From git by checking out the superproject? How would one figure out dependencies? How would one build libraries if he only has a partial checkout of Boost?  
  
We currently have no infrastructure for this, as far as I can tell. If checking out bits and pieces of Boost sufficient for a given use case is not a problem then running `b2 headers` or adding a few include paths in the user's project shouldn't be much of a problem too.  
  
And don't get me wrong, I want modular Boost too. But to me it's not clear how it will work. What is clear is that PRs like this, unfortunately, don't solve these problems and have the potential to impede a real solution. (Sorry, @Mike-Devel, I appreciate your effort.)  
  
Anyway, I really don't like being a dam in the way. If you're feeling strongly that we should go on and merge these PRs ASAP then could we at least settle with the minimal interface these CMakeFiles will provide, so that all proposed CMake solutions that we're about to review agree to support it? Could you post to the ML about this?

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-09 21:30:22 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428359652  

> How would one obtain these libraries?  
  
You add them as git submodules to your repo.  
  
> How would one figure out dependencies?  
  
By hand, I suppose. I mean, you want Filesystem, it's up to you to git submodule whatever it needs.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-10-09 21:46:28 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428364236  

Or, you could (eventually) just submodule the entire boostorg/boost and let it figure it out. -)

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-10-09 23:01:17 UTC  
> Updated_at: 2018-10-09 23:01:32 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428382062  

> For example, you took time to create PRs like this one, why not create a few similar PRs using an accepted solution? ;)  
  
If and when there is an accepted solution I'll certainly do that.   
  
To be completely blunt and at the risk of sounding arrogant: I don't think this recent  proposal by Robert had come if I hadn't started to make these PRs. At least to me as an outsider it didn't look as if anything was going to happened anytime soon - and not for lack of mature proposals.   
  
> @pdimov How would one obtain these libraries? From library git repo?   
  
Exactly (at least when you don't get it via a package manager)   
  
    git submodule add https://github.com/boostorg/config.git libs/boost_config  
    git submodule add https://github.com/boostorg/predef.git libs/boost_predef  
    git submodule add https://github.com/boostorg/winapi.git libs/boost_winapi  
    git submodule add https://github.com/boostorg/system.git libs/boost_system  
  
  
> How would one figure out dependencies?   
  
E.g. by running boostdep on a full release (of course you can also iteratively try to compile and add the libraries cmake is telling you you are missing) .  
  
> How would one build libraries if he only has a partial checkout of Boost?  
  
They are simply build as part of my cmake application just like any other open source dependencies. You don't need the boost super project for that.  
  
> If checking out bits and pieces of Boost sufficient for a given use case is not a problem then running b2 headers or adding a few include paths in the user's project shouldn't be much of a problem too.  
  
You don't strictly need the individual cmake files for the header only libraries, but the nice thing is that they provide a common interface. If I want to use functionality from library foo in my application bar, I write `target_link_libraries(bar foo)`, irrespective of whether it is header only or compiled or where it comes from and I e.g. also automatically get the include directories from the transitive dependencies (I would guess that boost build works similar in principle, just that someone made the decision to not explicitly document the dependencies between header only libraries and instead all of those files in a common include directory). Btw. having to run b2 header can be pretty annoying if you want to / have to modify an individual library.

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-10-09 23:41:09 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428389648  

> They are simply build as part of my cmake application just like any other open source dependencies.  
  
But your CMakeLists.txt does not support building.  
  
> You don't need the boost super project for that.  
  
As a side note, the superproject CMakeLists.txt is useful in other cases, like when you want to build the whole Boost, as you can do now. This is used when building Boost packages for Linux distros, for example.  
  
> Btw. having to run b2 header can be pretty annoying if you want to / have to modify an individual library.  
  
You don't have to run `b2 headers` when you modify a library, except for the rare case when you add a new header in a directory that is populated by multiple submodules, like boost/utility. And if you use Boost.Build to build Boost (which you pretty much have to now), I think, it is run automatically now anyway.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-10-10 07:04:33 UTC  
> Updated_at: 2018-10-10 07:34:06 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428461178  

> But your CMakeLists.txt does not support building.  
  
What is there to build? I thought system was a header only library by now.  
  
EDIT: Here is an example of a minim CMakeLists.txt file for a library that needs to be build: https://github.com/Mike-Devel/filesystem/blob/min_cmake/CMakeLists.txt

---

## Comment 13

> Username: Lastique  
> Created_at: 2018-10-10 09:27:59 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428503067  

I forgot that Boost.System was made header-only recently, sorry. But it actually still has a compiled part, see https://github.com/boostorg/system/blob/develop/src/error_code.cpp and https://github.com/boostorg/system/blob/develop/build/Jamfile.v2.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-10-10 09:54:14 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428511214  

The compiled part is just a compatibility stub so that people's code that links to System isn't broken.

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-10-10 10:47:12 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428526772  

Sure, but it needs to be build nonetheless.

---

## Comment 16

> Username: Mike-Devel  
> Created_at: 2018-10-10 10:53:00 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428528245  

> Sure, but it needs to be build nonetheless.  
  
Why?

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-10-10 11:15:35 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428533828  

For backward compatibility, no? If a project is linking against libboost_system and you don't want to break it as you upgrade to CMake, you should build it.

---

## Comment 18

> Username: Lastique  
> Created_at: 2018-10-10 14:19:53 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428590690  

I think the problem is that we come from different perspectives. I consider CMake to be an alternative to Boost.Build, which means that it should support the same use cases Boost.Build currently supports. CMake may offer additional benefits to downstream CMake users, but that is not what I'm concerned about.  
  
Currently, practically any project that uses Boost has to specify include paths and libraries to link manually. This won't change when the CMake support is released with Boost, so user projects will still be doing the same thing, at least for some time. In some cases, it won't change ever, if the project doesn't use CMake, for example.  
  
Packagers might want to switch from Boost.Build to CMake, though. This will remove Boost.System library from the build. In other words, building Boost with Boost.Build and CMake would result in a different set of libraries. IMHO, this is a problem because it effectively prevents packagers from using CMake. It is also a nuisance to the library maintainer in that he has to account for this difference between the build systems and respond to support requests regarding that difference.

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-10-10 15:01:35 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428607111  

If you're building the entire Boost with CMake as a replacement for the current `b2 install`, yes, it needs to build (and it's trivial to make it build.)  
  
For the use case in question, it doesn't, the user project links to Boost::system and it's declared as header only, so no library is added to the linker invocation.

---

## Comment 20

> Username: Mike-Devel  
> Created_at: 2018-10-10 15:28:07 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-428617811  

Sorry for the mixup with the different user accounts.  
  
>  consider CMake to be an alternative to Boost.Build, which means that it should support the same use cases Boost.Build currently supports.   
  
CMake is an alternative to boost build, but those minimal CMake files are not meant to be an alternative the the existing boost build files. They don't run unit tests, don't do name-mangling of the libraries, don't support installation, don't know about different boost versions... .  Instead their main purpose is to facilitating the local integration of individual boost libraries into a cmake based project.  
  
It is not that it would be difficult to add all those functionalities provided by the existing bjam files, but they come with a lot of design questions that should not be decided on a PR by PR basis and certainly not by an outsider like me that is not too familiar with the specifics of moest individual libraries and not necessarily a cmake expert. That is where the formal proposals and review process on the ML come into play. Of course each library maintainer is free to extend them such that they do provide a 1:1 experience to the boost build process, but for now I'm more interested in providing functionality that the existing process doesn't instead of replacing it.  
  
> Currently, practically any project that uses Boost has to specify include paths and libraries to link manually.   
  
Not at all. If they e.g. use cmake, at worst, they have to manually specify the boost root directory (if it isn't installed on the system and you don't use a package manager like vcpkg or conan). And I'm pretty sure if I'm using some other build system like meson, build2 or even boost build I'll also not have to manually specify the individual library files and include directories (if you are writing make-files you can use pkgconfig to achieve the same effect). With vcpkg I can even use boost from a normal MSVC project without specifying my boost dependencyies anywhere in the project configuration (which makes me actually quite unhappy, but that is a different topic).   
  
> Packagers might want to switch from Boost.Build to CMake, though.   
  
Yes they might want to do that, but again. This cmake file - in its current form - is completely unsuited for that task, as it doesn't support installation or name mangling.  
  
> It is also a nuisance to the library maintainer in that he has to account for this difference between the build systems and respond to support requests regarding that difference.  
  
That is another reason why I kept those files deliberately so simple, short and limited - but again, each library maintainer is of course free to extend them or just wait for the review.

---

## Comment 21

> Username: Mike-Devel  
> Created_at: 2018-10-13 06:58:17 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-429517101  

Ok, so for now I just implemented option 6 as a fallback.

---

## Comment 22

> Username: Mike-Devel  
> Created_at: 2018-10-31 10:21:21 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-434634310  

Is there any chance this is going to be merged without winapi having a CmakeLists file or are we waiting to see what becomes of Robert's plan?

---

## Comment 23

> Username: Mike-Devel  
> Created_at: 2018-12-23 21:53:12 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-449665393  

Ok, winapi has a cmake file, so no fallback logic is necessary, but as Boost.Predef doesn't have one yet (https://github.com/boostorg/predef), this PR should probably still not be merged.

---

## Comment 24

> Username: Mike-Devel  
> Created_at: 2019-01-02 21:24:01 UTC  
> Url: https://github.com/boostorg/system/pull/27#issuecomment-450989512  

Now that all dependencies (config, predef, winapi) have a cmake file this PR is safe to merge.

---
