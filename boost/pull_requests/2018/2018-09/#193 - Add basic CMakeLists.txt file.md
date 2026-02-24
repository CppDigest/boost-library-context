# #193 [CMake] Add basic CMakeLists.txt file [Closed]

> Username: Mike-Devel  
> Created at: 2018-09-14 13:09:09 UTC  
> Updated at: 2019-12-16 09:14:22 UTC  
> Closed at: 2019-12-16 09:14:21 UTC  
> Url: https://github.com/boostorg/boost/pull/193  

The file just processes all CMakeLists.txt files  
that it findes in one of the library or tool root  
directories.  
  
Further reference: https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2018-09-19 16:30:15 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-422869600  

From the ML https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M (From James E. King III ):  
  
> Absolutely, positively do NOT use GLOB for project build chains or for  
> header or source discovery.  
> There are well documented reasons not to do this.  You must express the  
> dependency chain in  
> linear order in libs/CMakeLists.txt.  
>   
> I would have the boostorg/CMakeLists.txt call:  
>   
> add_subdirectories ( libs )  
> after it has included all the things libs depends on.  
>   
> Then have libs/CMakeLists.txt call add_subdirectories in dependency order.  
> That is the only way you can reasonably ensure a linear dependency chain.  
  
So if that is what you want, I can turn the globbing into an explicit list of libraries.  
  
However, before I do that, could soemone please explain more detailed what specifically are the problems with globbing in the particular context of this CMakeLists.txt file. I believe I know all the arguments for not globbing for source files but imho most don't really apply here.  
  
1) CMake is perfectly capable of resolving dependencies it sees in reverse order. So I don't know, why the libraries need to be passed in dependency order (maintaining that order correctly seems to be even more work than maintaining the list of libraries that do have a CMakeLists.txt file - who would maintain it?)  
2) Not globbing means that every time a new library adds a cmake file and wants   
   to hook up to the project wide mechanism, a new PR to the top level project   
   needs to be merged (in addition to any automatic update of the submodule).   
   I can see that this can be seen as an advantage as well as a disadvantage,  
   but wanted to call it out regardless.  
3) Even if no globbing is being used, there should be a mechanism to only add  
   a subdirectory if the folder/CMakeFile it is actually present (often I don't   
   want to clone all of boost but just a defined subset).   
  
Regardless of globbing/non-globbing I intent to implement a blacklist that lists ignored libraries (e.g. because their CMakeLists.txt file doesn't play nice - yet).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-09-27 22:25:29 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-425262142  

See: https://stackoverflow.com/questions/1027247/is-it-better-to-specify-source-files-with-glob-or-each-file-individually-in-cmak/1060061  
  
Specifically, the authors of CMake say this, which I have seen first-hand:  
  
> (We do not recommend using GLOB to collect a list of source files from your source tree. If no CMakeLists.txt file changes when a source is added or removed then the generated build system cannot know when to ask CMake to regenerate.)  
  
I assume the same would be true for subdirectories as well.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-09-28 06:17:23 UTC  
> Updated_at: 2019-03-31 15:37:29 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-425333327  

> I assume the same would be true for subdirectories as well.  
  
Imho this is a completely different situation for the reasons I explained above. Most importantly the support for partial checkout and because the person that adds the CMakeFile (the library maintainer) usually doesn't have the rights to modify this top level cmake file accordingly.  
  
Also, if I translate above quote to the current situation it would read:  
  
> If the top level CMakeFile doesn't change when a new library CMakeFile is added or removed then the generated build system cannot know when to ask CMake to regenerate.)  
  
In other words: cmake needs to be called again manually when a new cmake file is added or moved (not when one is changed). How is that a problem and is the alternative really preferable?  
  
I guess what I'm asking for is a different explanation than "I've heard globbing (in a different context) is discouraged"

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-30 03:57:05 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-425692679  

I'm not sure that we want to include the tools here. At some point, we want `cmake --build . --target install` to perform the equivalent of `b2 install`, and that doesn't install any tools. That's probably for the better, as their names are too generic for `bin` (except perhaps for `boostdep` :-) ).  
  
For installing the tools, I think that we'll be fine with supporting that only from the appropriate tool's directories, one by one, without having a superproject batch tool install.  
  
Maybe we do need to test the tools as part of `cmake --build . --target check` though. Without knowing what our testing setup is going to look like, it's hard to tell. We could accumulate all the tests at once, or we could `ctest` the libraries one by one in the `check` target.  
  
In either case, it might be better to split that into `libs/CMakeLists.txt` and `tools/CMakeLists.txt` and only leave the two `add_subdirectory` calls here (if we even have `tools/CMakeLists.txt` at all).  
  
I'm not particularly fond of the variables either. We should just have a hardcoded blacklist. We know which libraries don't work.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2018-10-16 09:08:50 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-430161175  

> I'm not sure that we want to include the tools here. At some point, we want cmake --build . --target install to perform the equivalent of b2 install, and that doesn't install any tools. That's probably for the better, as their names are too generic for bin (except perhaps for boostdep :-) ).  
  
Well, at least some tools use boost libraries, so they could profit from the targets made available via this super-project. For now I'm just deactivating tool building by default, but if you feel strongly about this I'm fine with completely removing them  
  
> For installing the tools, I think that we'll be fine with supporting that only from the appropriate tool's directories, one by one, without having a superproject batch tool install.  
  
> Maybe we do need to test the tools as part of cmake --build . --target check though. Without knowing what our testing setup is going to look like, it's hard to tell. We could accumulate all the tests at once, or we could ctest the libraries one by one in the check target.  
  
Well, installation and testing is a whole nother can of worms that will hopefully get resolved during the cmake review on the ML. I don't really expect this file to live past that review.   
  
  
> In either case, it might be better to split that into libs/CMakeLists.txt and tools/CMakeLists.txt and only leave the two add_subdirectory calls here (if we even have tools/CMakeLists.txt at all).  
  
I can do that, but personally I don't think the file is complex enough yet to warrant the splitup (People then have to look at three files instead of one to understand what is going on).  
  
> I'm not particularly fond of the variables either. We should just have a hardcoded blacklist. We know which libraries don't work.  
  
Not really. For one, compatibility in individual libraries is currently a moving target. Having (only) a fixed black list would require a lot of PRs to the super project. Second, some libraries are in principle usable, but mutually exclusive (name collision). Third, compatibility also depends on what branches are currently checked out and which target & toolchain they are compiled for / with.  
  
Of course that doesn't mean there shouldn't be a sensible (& conservative) default, but having a easy option to override this list without changing the actual file can be usefull during development (remember that this file currently isn't really useful for the "normal" end-user anyway, as very few libraries support CMake in the first place).

---

## Review 6 [Commented]

> Username: orgads  
> Created_at: 2019-06-24 17:38:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/193#pullrequestreview-253557256  

📁 CMakeLists.txt

```diff
  25 |+ =                                                                                                =
  26 |+ = General cmake support in boost is EXPERIMENTAL at best and                                     =
  27 |+ = only a few libraries can be build via cmake at all.                                            =
```

> Username: orgads  
> Created_at: 2019-06-24 17:37:14 UTC  
> Updated_at: 2019-06-24 17:38:38 UTC  
> Url: https://github.com/boostorg/boost/pull/193#discussion_r296835581  

built

> Username: Mike-Devel  
> Created_at: 2019-06-25 06:15:31 UTC  
> Url: https://github.com/boostorg/boost/pull/193#discussion_r297021043  

Will fix

---

📁 CMakeLists.txt

```diff
   1 |+ # Copyright 2018 Mike Dev
```

> Username: orgads  
> Created_at: 2019-06-24 17:38:31 UTC  
> Updated_at: 2019-06-24 17:38:38 UTC  
> Url: https://github.com/boostorg/boost/pull/193#discussion_r296836107  

2019

> Username: Mike-Devel  
> Created_at: 2019-06-25 06:15:19 UTC  
> Url: https://github.com/boostorg/boost/pull/193#discussion_r297020994  

Well, the file was created in 2018. But as the have been modifications in 2019 I probably should add that year.


📁 libs/numeric/CMakeLists.txt

```diff
   1 |+ # Copyright 2018 Mike Dev
```

> Username: orgads  
> Created_at: 2019-06-24 17:38:23 UTC  
> Updated_at: 2019-06-24 17:38:38 UTC  
> Url: https://github.com/boostorg/boost/pull/193#discussion_r296836058  

2019


---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2019-12-16 09:14:21 UTC  
> Url: https://github.com/boostorg/boost/pull/193#issuecomment-565972526  

Superseded by pdimov's addition. Thanks!

---
