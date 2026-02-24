# #233 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:52 UTC  
> Updated at: 2025-05-25 21:48:26 UTC  
> Merged at: 2025-05-25 21:48:16 UTC  
> Closed at: 2025-05-25 21:48:16 UTC  
> Url: https://github.com/boostorg/log/pull/233  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:43:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/log/pull/233#pullrequestreview-2190078483  

📁 build.jam

```diff
   8 |+ project /boost/log
   9 |+     : common-requirements
  10 |+         <library>/boost/align//boost_align
```

> Username: Lastique  
> Created_at: 2024-07-20 23:29:05 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551134  

Same as for other built libraries, those should probably be attached to the respective libraries (which there are two for Boost.Log) and public and private dependencies separate.

---

📁 build.jam

```diff
  54 |+ use-project /boost/log/regex-header-only : config/regex-header-only ;
  55 |+ use-project /boost/log/x86-extensions : config/x86-ext ;
  56 |+ use-project /boost/log/xopen-source-600 : config/xopen-source-600 ;
```

> Username: Lastique  
> Created_at: 2024-07-20 23:35:31 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551668  

Why are these here? Those are config checks needed for building the library. I would expect these projects to be referenced in `build/Jamfile.v2` or one of the `build/*-config.jam` files.


📁 build/Jamfile.v2

```diff
  56 |     : source-location ../src
  57 |     : requirements
  63 |-         <conditional>@log-arch-config.check-instruction-set
```

> Username: Lastique  
> Created_at: 2024-07-20 23:32:02 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551379  

Why is this removed?

---

📁 build/Jamfile.v2

```diff
 210 | 
 217 |-     if x86 in [ log-arch-config.deduce-architecture $(properties) ]
 211 |+     if <architecture>x86 in $(properties)
```

> Username: Lastique  
> Created_at: 2024-07-20 23:35:46 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551692  

Is this correct? This must work as expected even if `<architecture>` is not specified by user. Same with the similar change re. `<address-model>` below.

> Username: grafikrobot  
> Created_at: 2024-07-26 01:38:18 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1692367696  

It's auto-detected and set now. So this should always be valid.


📁 build/log-arch-config.jam

```diff
 171 |-     }
 172 |- 
 173 |-     return $(result) ;
```

> Username: Lastique  
> Created_at: 2024-07-20 23:33:35 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551565  

Why is this removed? And an empty rule is left, too?

---

📁 build/log-arch-config.jam

```diff
  16 | rule ssse3-flags ( properties * )
  17 | {
  97 |-     local result ;
```

> Username: Lastique  
> Created_at: 2024-07-20 23:36:40 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551744  

This looks incorrect, `result` is used below.

---

📁 build/log-arch-config.jam

```diff
  92 |-     return $(result) ;
  93 |- }
  94 |- 
```

> Username: Lastique  
> Created_at: 2024-07-20 23:37:07 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551762  

Why are these rules removed?


📁 build/log-build-config.jam

```diff
  22 |- project.load [ path.join [ path.make $(here:D) ] ../config/regex-header-only ] ;
  23 |- project.pop-current ;
  24 |- 
```

> Username: Lastique  
> Created_at: 2024-07-20 23:34:26 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551609  

Please, keep the config stuff localized here.

> Username: grafikrobot  
> Created_at: 2024-07-26 01:39:45 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1692368457  

This is no longer needed. As there is no project in those other modules. They are plain b2 modules.


📁 build/log-platform-config.jam

```diff
  18 |- project.load [ path.join [ path.make $(here:D) ] ../config/xopen-source-600 ] ;
  19 |- project.pop-current ;
  20 |- 
```

> Username: Lastique  
> Created_at: 2024-07-20 23:39:36 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685551957  

Ditto.

> Username: grafikrobot  
> Created_at: 2024-07-25 13:51:56 UTC  
> Updated_at: 2024-07-25 13:51:57 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1691484171  

An FYI.. This is no longer needed because rooted project/target references (like `/boost/log/config/xopen-source-600//xopen_source_600`) are automatically resolved and loaded on demand.


📁 example/wide_char/Jamfile.v2

```diff
  44 |         <library>/boost/filesystem//boost_filesystem
  45 |-         <library>/boost/locale//boost_locale
  45 |+         <library>/boost/locale//boost_locale/<link>static
```

> Username: Lastique  
> Created_at: 2024-07-20 23:40:15 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685552064  

Why is this change needed?


📁 test/Jamfile.v2

```diff
  75 |-         local headers_path = [ path.make $(BOOST_ROOT)/libs/log/include/boost/log ] ;
  76 |-         for file in [ path.glob-tree $(headers_path) : *.hpp : detail ]
  75 |+         for file in [ glob-tree-ex ../include/boost/log : *.hpp : detail ]
```

> Username: Lastique  
> Created_at: 2024-07-20 23:43:33 UTC  
> Updated_at: 2024-07-20 23:43:52 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1685552452  

What is `glob-tree-ex` and how is it different from `path.glob-tree`? Is this change necessary?  
  
Also, I would prefer to keep the `headers_path` variable to avoid path duplication.

> Username: grafikrobot  
> Created_at: 2024-07-25 13:56:20 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1691491557  

`path.glob-tree` operates on paths not normalized to the current project. Hence why this had absolute paths rooted at `$(BOOST_ROOT)`. But we can't rely on `BOOST_ROOT` in the modular layout as it may not exist. `glob-tree-ex` is equivalent, but operates on paths relative to the current project (and is a project only rule). Hence it will find things without having to figure out some absolute path.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-07-24 22:54:35 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2249026008  

General FYI.. I'm doing changes to the PR to account for your feedback (and all the other feedback in other PRs). Again, thanks for taking a careful look. Much appreciated!

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-08-18 15:53:21 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2295308565  

Please review and merge this PR at your earliest convenience.

---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2024-08-18 23:53:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/log/pull/233#pullrequestreview-2244436610  

📁 build.jam

```diff
  38 |+     /boost/utility//boost_utility
  39 |+     /boost/winapi//boost_winapi
  40 |+     /boost/xpressive//boost_xpressive ;
```

> Username: Lastique  
> Created_at: 2024-08-18 23:42:58 UTC  
> Updated_at: 2024-08-18 23:53:31 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721089334  

Boost.IO is not a public dependency.  
  
Some of these dependencies are conditional and only present depending on the platform or build configuration.  
  
And most importantly, the public dependencies need to be separate for `log` and `log_setup`. Preferably also separate `log` and `log_with_support`, as it is done in CMakeLists.txt.

> Username: grafikrobot  
> Created_at: 2024-08-19 01:00:31 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721109774  

I haven't dealt at all with optional dependencies in the modular work. As a main goal is for package management resolution. For which optional dependencies are considered a bad practice.  
  
Yes, the dependencies could be refined to each target. But I'm not familiar enough with the library, or the cmake in this, to do that in these changes. Maybe something to do in the near future.

> Username: Lastique  
> Created_at: 2024-08-19 08:12:38 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721378212  

> I haven't dealt at all with optional dependencies in the modular work. As a main goal is for package management resolution. For which optional dependencies are considered a bad practice.  
  
I'd say it's the opposite, as this is a way to reduce dependencies for users to satisfy.  
  
Anyway, the dependencies are currently optional and separate for `log` and `log_setup`. I'd like it to remain this way after modularization.  
  
There is no `log_with_support` in b2 currently because there was no point in it as the extra dependencies are header-only, and b2 didn't specify those before. You PR adds header-only dependencies, so it makes sense to introduce this target. Yes, this is new to b2, so I'm not strictly requiring it, but it would be nice to have.

> Username: grafikrobot  
> Created_at: 2024-09-05 05:47:50 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1744838057  

I'll add the `log_with_support` target in the next set of changes.

> Username: grafikrobot  
> Created_at: 2024-09-05 14:19:18 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1745637887  

Added the log_with_support target.


📁 build/Jamfile.v2

```diff
  64 |+ project
  65 |     : source-location ../src
  66 |+     : common-requirements <library>$(boost_dependencies)
```

> Username: Lastique  
> Created_at: 2024-08-18 23:45:57 UTC  
> Updated_at: 2024-08-18 23:53:31 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721089849  

I think, dependency `requirements` and `usage-requirements` need to be specified per-library rather than for the project, as these requirements are different.

---

📁 build/Jamfile.v2

```diff
 319 |         <link>shared:<define>BOOST_LOG_DYN_LINK=1
 320 |         <threading>single:<define>BOOST_LOG_NO_THREADS
 321 |+         <define>BOOST_LOG_NO_LIB=1
```

> Username: Lastique  
> Created_at: 2024-08-18 23:46:54 UTC  
> Updated_at: 2024-08-18 23:53:31 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721090038  

Is this intentional? Same re. `BOOST_LOG_SETUP_NO_LIB` below.

> Username: grafikrobot  
> Created_at: 2024-08-19 00:47:47 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721105026  

And I finally remembered the logic. Yes it's intentional. It turns off the auto linking. Which is not needed if one is using B2, or any other build system, as it will add in the appropriate link flags.


📁 config/build.jam

```diff
  12 |+         [ predef.address-model ]
  13 |+         [ predef.architecture ]
  14 |+     ;
```

> Username: Lastique  
> Created_at: 2024-08-18 23:52:26 UTC  
> Updated_at: 2024-08-18 23:53:31 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721091134  

What is the purpose of this file? Is it actually needed?

> Username: grafikrobot  
> Created_at: 2024-08-19 00:54:00 UTC  
> Updated_at: 2024-08-19 00:54:01 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721107516  

It sets the `address-model` and `architecture` features to the platform detected values for all the config subprojects. Which is what allows this https://github.com/boostorg/log/pull/233/files#diff-b200e03281a7c4df9a1bfad85a764447a1a56954a2efc88f8ba8c212acb38686R18 to be just fetching the feature value.

> Username: Lastique  
> Created_at: 2024-08-19 08:20:53 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r1721392433  

I thought you said these properties are auto-detected and always set now, are they not?  
  
Config projects are used from the main build Jamfile, are these (and all other) properties not forwarded to the config projects when they are compiled? I think, they need to be.

> Username: grafikrobot  
> Created_at: 2025-05-16 04:21:20 UTC  
> Url: https://github.com/boostorg/log/pull/233#discussion_r2092293164  

The predef.address-model and predef.architecture is the auto-detection. We can't rely on the boost-root/Jamfile to propagate such information as that build file may not exist when this library is externally consumed in a modular way. When I said auto-detected I was comparing it to what you previously did to detect and respond to the settings in the various support jam code. Which all got replaces with the use of the more accurate predef detection.


---

## Comment 5

> Username: Lastique  
> Created_at: 2024-10-10 14:45:41 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2405316286  

Is this still pending some changes? If it is, please, convert it to a draft.

---

## Comment 6

> Username: Lastique  
> Created_at: 2025-04-05 17:40:10 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2781011400  

@grafikrobot Note that clang-cl build [failed](https://ci.appveyor.com/project/Lastique/log/builds/51831676/job/qb9kpwd9qn9fjk7x#L559) in AppVeyor CI.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2025-04-05 22:08:03 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2781111771  

> @grafikrobot Note that clang-cl build [failed](https://ci.appveyor.com/project/Lastique/log/builds/51831676/job/qb9kpwd9qn9fjk7x#L559) in AppVeyor CI.  
  
Ah.. That's actually Context failing to build.

---

## Comment 8

> Username: Lastique  
> Created_at: 2025-04-05 22:17:50 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2781114426  

> > @grafikrobot Note that clang-cl build [failed](https://ci.appveyor.com/project/Lastique/log/builds/51831676/job/qb9kpwd9qn9fjk7x#L559) in AppVeyor CI.  
>   
> Ah.. That's actually Context failing to build.  
  
Yes, but current develop did [succeed](https://ci.appveyor.com/project/Lastique/log/builds/51832695). Only this PR failed.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2025-04-05 22:20:08 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2781114951  

> > > @grafikrobot Note that clang-cl build [failed](https://ci.appveyor.com/project/Lastique/log/builds/51831676/job/qb9kpwd9qn9fjk7x#L559) in AppVeyor CI.  
> >   
> >   
> > Ah.. That's actually Context failing to build.  
>   
> Yes, but current develop did [succeed](https://ci.appveyor.com/project/Lastique/log/builds/51832695). Only this PR failed.  
  
Sorry.. That was my inner brain typing things out. Yes. It's a problem I need to figure out here. It's just the problem is likely something with how to use Context lib.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2025-05-16 04:16:20 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2885588358  

@Lastique this https://github.com/boostorg/context/pull/304 fixes the issue with context failing and hence causing the failure here.

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2025-05-25 21:22:49 UTC  
> Updated_at: 2025-05-25 21:23:38 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2908089492  

The clang-win tests now also pass.. https://ci.appveyor.com/project/Lastique/log/builds/52118081

---

## Comment 12

> Username: Lastique  
> Created_at: 2025-05-25 21:48:25 UTC  
> Url: https://github.com/boostorg/log/pull/233#issuecomment-2908105818  

Thank you.

---
