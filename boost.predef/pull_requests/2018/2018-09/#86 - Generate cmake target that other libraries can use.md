# #86 [CMake] Generate cmake target that other libraries can use [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-17 09:35:31 UTC  
> Updated at: 2019-01-02 17:21:17 UTC  
> Merged at: 2019-01-02 13:26:49 UTC  
> Closed at: 2019-01-02 13:26:49 UTC  
> Url: https://github.com/boostorg/predef/pull/86  

... to express their dependency on this library and retrieve any  
configuration information such as the include directory, binary  
to link to (if any), necessary compiler options or the required  
c++ standards level.

---

## Review 1 [Commented]

> Username: grafikrobot  
> Created_at: 2018-09-22 04:11:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/86#pullrequestreview-157898562  

📁 CMakeLists.txt

```diff
   4 |+ 
   5 |+ cmake_minimum_required(VERSION 3.5)
   6 |+ project(BoostPredef LANGUAGES CXX)
```

> Username: grafikrobot  
> Created_at: 2018-09-22 04:11:53 UTC  
> Updated_at: 2019-01-01 22:46:29 UTC  
> Url: https://github.com/boostorg/predef/pull/86#discussion_r219660942  

Predef is a C, C++, Objective-C, and Objective-C++ library.

> Username: Mike-Devel  
> Created_at: 2018-09-22 11:22:27 UTC  
> Updated_at: 2019-01-01 22:46:29 UTC  
> Url: https://github.com/boostorg/predef/pull/86#discussion_r219668896  

Ok.  
I don't think cmake has an dedicated objective C mode so I just removed the explicit mentioning of any languages. At the moment it doesn't really matter anyway.

> Username: jeking3  
> Created_at: 2018-11-01 20:11:36 UTC  
> Updated_at: 2019-01-01 22:46:29 UTC  
> Url: https://github.com/boostorg/predef/pull/86#discussion_r230180942  

"C CXX" seems appropriate...

> Username: Mike-Devel  
> Created_at: 2018-11-02 11:53:21 UTC  
> Updated_at: 2019-01-01 22:46:29 UTC  
> Url: https://github.com/boostorg/predef/pull/86#discussion_r230348985  

@jeking3: That is the default anyway. Just specifying CXX has the advantage that cmake doesn't look for a c compiler at all, which speeds up the process minimally, but I don't see the advantage in adding C CXX explicitly.   
That being said, if you like the explicit form more, I'm happy to add it.

> Username: Mike-Devel  
> Created_at: 2018-11-02 11:54:45 UTC  
> Updated_at: 2019-01-01 22:46:29 UTC  
> Url: https://github.com/boostorg/predef/pull/86#discussion_r230349271  

Btw.: As this cmake file doesn't build anything, one could even pass `NONE` as a language parameter, but again I don't see the advantage.


---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-10-09 09:41:23 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-428128324  

Anything else you want me to change?

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-12-08 11:13:33 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-445451434  

Ping

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-12-18 17:39:44 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-448306012  

@grafikrobot: As similar PRs in other libraries will depend/are depending on this PR, it would be nice to know, if you are planning to accept this PR at all (or add a similar file yourself) or not. Otherwise I'll need to implement a workaround on dependent libraries.  
  
If there are any changes you'd like to see (let travis test the Cmake file), I'd be happy to oblige

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2019-01-01 17:04:28 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-450743591  

Sorry @Mike-Devel for the delays on this.. I was hoping that the current cmake effort would get farther before having to decide on this. But I don't think that's going to see movement for a while :-(  
  
For me to accept another build system I'd need need a few more things. Especially since I'll have to maintain this going forward.  
  
1. A well documented file. I'm not well versed in the many cmake ways. Hence I would need to know what each line does and why. For an example see here https://github.com/boostorg/predef/blob/develop/doc/build.jam#L61  
2. What use cases is this for? In particular if this is for standalone use? I ask because I mirror predef for non-Boost use here https://github.com/grafikrobot/hash-predef -- and I'd like that to also have cmake support.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2019-01-01 17:10:08 UTC  
> Updated_at: 2019-01-01 17:10:22 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-450743915  

In addition it would be fantastic if there was also some form of CI testing to ensure the cmake support works. Something as simple as a script, plus supporting files, for me to run on Travis & Appveyor would be good.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2019-01-01 22:42:16 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-450762450  

> Sorry @Mike-Devel for the delays on this.. I was hoping that the current cmake effort would get farther before having to decide on this. But I don't think that's going to see movement for a while :-(  
  
No worries, I hoped that too. I believe/hope that nothing my minimal cmake script does will conflict with anything required by a full-flegeded, boost-wide solution if and when that comes. The only bit of public interface that should be kept unchanged under maintenence is the target name `Boost::predef`.  
  
> For me to accept another build system I'd need need a few more things. Especially since I'll have to maintain this going forward.  
>  
>    1. A well documented file. I'm not well versed in the many cmake ways. Hence I would need to know what each line does and why. For an example see here https://github.com/boostorg/predef/blob/develop/doc/build.jam#L61  
  
I added a note to the beginnig to the file and if you insist I can add a per-line comment, but I don't think, they would be particularly sensible here as they would be the equivalen to   
  
    i++; // increment i  
  
I deliberately kept those files simple in scope, such that non-cmake users should be able to easily understand them an - at the worst - have to look up the documentation for each of the 5 commands, which should not take too much time. In case you are wondering: I chose cmake 3.5 as a minimum, because it is what's available on ubuntu xenial and I didn't run any tests with older cmake versions. I think it currently doesn't actually use any cmake features beyond what is available in 3.0.   
  
If you have any questions about a line in the cmake file, it might be best to discuss them here and you add whatever comments you feel necessary  
  
>    What use cases is this for? In particular if this is for standalone use? I ask because I mirror predef for non-Boost use here https://github.com/grafikrobot/hash-predef -- and I'd like that to also have cmake support.  
  
pdimov gave a good, concise explanation here: https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
  
The idea is that you have a cmake - based project where you want to use `Boost.predef`, so you clone it into some local folder. Then you write `add_subdirectory(<path-to-predef)` into your config file and e.g. `target_link_libraries( <my-exec> PUBLIC Boost::predef ) to "link" predef to your executable or library (as predef is header only, all it will actually do is provide the correct include paths to the compiler).  
  
> In addition it would be fantastic if there was also some form of CI testing to ensure the cmake support works. Something as simple as a script, plus supporting files, for me to run on Travis & Appveyor would be good.  
  
I added a test to travis which essentially tries to build an executable that includes the boost predef headers just as described above. Unfortunately, I'm much less well versed in Appveyor, so I'm not sure how to integrate it there, but I would guess that there is a relatively easy 1:1 translation from travis possible for anyone familar with both ci systems.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2019-01-01 22:43:13 UTC  
> Url: https://github.com/boostorg/predef/pull/86#issuecomment-450762497  

No Idea, why Appveyor fails, as I haven't touched the `appveyor.yml` file.

---
