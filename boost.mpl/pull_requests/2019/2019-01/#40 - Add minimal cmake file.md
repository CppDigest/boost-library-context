# #40 Add minimal cmake file  [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-04 13:32:27 UTC  
> Updated at: 2019-03-07 13:13:29 UTC  
> Merged at: 2019-02-14 00:06:09 UTC  
> Closed at: 2019-02-14 00:06:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/40  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
    add_subdirectory( <path-to-boost_mpl> )  
    target_link_libraries( <my_lib> PUBLIC Boost::mpl )  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
- https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Note: I haven't yet hooked up the tests to travis for two reasons:  
  
1) frankly, the travis file looks much more complicated than what I'm used to so I'm not sure how to best integrate it here. Any pointers are welcomed  
2) One of the transitive dependencies (container_hash) doesn't have a cmake file yet (although https://github.com/boostorg/container_hash/pull/5 changes that). So the test would fail anyway.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-01-04 15:30:42 UTC  
> Updated_at: 2019-02-13 01:18:03 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-451476181  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=h1) Report  
> Merging [#40](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/mpl/commit/ac67f5952e6c849e461d7651ffb4fa0e7f380bb0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/40/graphs/tree.svg?width=650&token=31bBblRchG&height=150&src=pr)](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #40   +/-   ##  
========================================  
  Coverage    73.33%   73.33%             
========================================  
  Files            5        5             
  Lines           15       15             
  Branches         3        3             
========================================  
  Hits            11       11             
  Misses           1        1             
  Partials         3        3  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=footer). Last update [ac67f59...136d323](https://codecov.io/gh/boostorg/mpl/pull/40?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2019-01-05 00:09:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpl/pull/40#pullrequestreview-189570153  

📁 CMakeLists.txt

```diff
  14 |+ target_include_directories( boost_mpl INTERFACE include )
  15 |+ 
  16 |+ target_link_libraries( boost_mpl
```

> Username: jeking3  
> Created_at: 2019-01-05 00:09:45 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r245453842  

How do we keep this list updated?  Can the b2 build fail if this list is not equal to the actual primary dependencies?

> Username: Mike-Devel  
> Created_at: 2019-01-05 07:24:58 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r245470014  

Good question. Responded in https://github.com/boostorg/logic/pull/14#issuecomment-451634586

> Username: Mike-Devel  
> Created_at: 2019-01-05 07:25:26 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r245470028  

Good question. Responded in https://github.com/boostorg/logic/pull/14#issuecomment-451634586

> Username: jeking3  
> Created_at: 2019-01-24 04:04:38 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250460138  

I don't see the list of dependencies being static as acceptable.  They change all the time, but I don't see a solution here.

> Username: Mike-Devel  
> Created_at: 2019-01-24 09:11:21 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250516707  

>  They change all the time,   
  
Really? Is there so much active development in boost.mpl? Keep in mind that this file only specifies direct depencies.  
  
Personally, I think adding dependencies to a library willy nilly is an absolute no-go anyway.

> Username: jeking3  
> Created_at: 2019-01-24 17:35:45 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250707288  

What if someone checks in a small bugfix to mpl and I commit it, and it pulls in a new header and a new library, which pulls in three others... a static list is not maintainable.  Whether or not the repository is under active development or not is another matter.  This pattern will be copied into repositories that are active, and then it will not be maintainable.

> Username: Mike-Devel  
> Created_at: 2019-02-05 19:42:18 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r254017866  

@jeking3: Sorry for not answering earlier, but life happened.  
  
> What if someone checks in a small bugfix to mpl and I commit it, and it pulls in a new header and a new library  
  
I can just repeat myself: A small bugfix should not change the list of dependencies.   
Also, it is not as if this would not be detectable: E.g. through build tests, manual/automatic comparison of the output of boostdep with the cmake file or simply by checking the patch for new `#include` statements.  
  
> which pulls in three others...  
  
This file is only concerned with the direct dependencies not the transitive ones  
  
> a static list is not maintainable  
  
All the projects I've been working on have used static lists of dependencies in one form or another (If only a static list of include directories and binaries to link with) and if I'm not mistaken, so do compiled boost libraries in their jamfiles, the other boost libraries that have gained cmake files so far and an best I can tell, full fledged solutions like bcm do this too (e.g. https://github.com/boost-cmake/boost/blob/master/libs/mpl/CMakeLists.txt).   
  
So I really don't agree with that statement and actual think that a static list of dependencies is the norm rather than the exception.  
  
However, in the end, the only thing that matters is what you are willing to merge into your library. So what specifically is needed to make this acceptable for you?   
  
>  This pattern will be copied into repositories that are active, and then it will not be maintainable.  
  
I can understand that manual maintainence is a problem for CMT libraries, but for a repository under active development, updating the cmake file every now and then should really be the least  of the problems (It seems to me that the newest / most actively maintained libraries use cmake for the develoment anyway).


---

## Comment 3

> Username: eldiener  
> Created_at: 2019-01-16 14:40:16 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-454802667  

Is this ready to be merged in its present form ?

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-01-16 19:21:27 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-454905669  

> Is this ready to be merged in its present form ?  
Well, my notes still apply:  
  
https://github.com/boostorg/container_hash/pull/5 is still not merged yet, so it won't work and right now I can't verfiy that the test works once the PR in container_has does get imerged (I think I tested it locally, when I added the test it but not 100% sure anymore).   
  
However, it won't break anything (as it isn't hooked up to the ci) and it wouldn't be the first cmake file that gets added before all its dependencies are there.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-01-23 17:46:32 UTC  
> Updated_at: 2019-01-24 09:27:25 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-456898980  

@eldiener: https://github.com/boostorg/container_hash/pull/5 has now been merged and I tested the PR locally again.   
  
Everything seems to be in order so the PR can be merged.  
  
If you only want to merge the plain cmake file without the selftest plumbing (as e.g. prefered by peter dimov) you or I can remove the last commit form this PR  (`ddb5c9b [CMake] Add test for cmake file`)

---

## Review 6 [Changes requested]

> Username: jeking3  
> Created_at: 2019-01-24 04:07:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/mpl/pull/40#pullrequestreview-195849751  

I have no issues merging this if there are issues open to resolve the problems I see will occur as a result of having static dependency lists in the CMake files.

📁 test/test_cmake/CMakeLists.txt

```diff
  10 |+ project( BoostBindCMakeSelfTest )
  11 |+ 
  12 |+ add_subdirectory( ../../../assert ${CMAKE_CURRENT_BINARY_DIR}/libs/assert )
```

> Username: jeking3  
> Created_at: 2019-01-24 04:05:14 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250460222  

Static list of dependencies is going to cause long-term maintenance problems.

> Username: Mike-Devel  
> Created_at: 2019-01-24 08:52:42 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250510695  

Two reasonable alternatives I see:   
  
- Just remove the test altogether- that is what peter dimov argues for  
- Instead of listing them explicitly,  grep for all sibling projects that have a cmake file and assume only the necessary repositories are checked out.


📁 test/test_cmake/main.cpp

```diff
   1 |+ // make sure we find all transitively included headers
   2 |+ 
   3 |+ #include <boost/mpl/accumulate.hpp>
```

> Username: jeking3  
> Created_at: 2019-01-24 04:06:37 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250460432  

It would be better to make a build directive to include each file separately and make sure it compiles, like the one in Boost.Uuid test jam, which I got from @lastique, I believe?

> Username: Mike-Devel  
> Created_at: 2019-01-24 08:43:30 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250507955  

The purpose of the test is to make sure each (directly or indirectly) included headerfile can be found (showing that cmake knows about all dependencies).   
  
Not to test the proper design of the header files - that is an completely orthogonal issue that is covered by the b2 driven unit tests. You can of course add the same functionality to cmake if you want, but I'd rather deal with that separately.

> Username: Lastique  
> Created_at: 2019-01-24 08:57:25 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250512174  

I think, the point here is that it's better to avoid manual enumeration of the headers and generate it based on the filesystem. It's not exactly what we do to verify self-sufficiency of headers, but the implementation could be similar: generate a Boost.Preprocessor sequence of headers and expand it to includes in the .cpp. Or you could simply generate the entire .cpp file from CMake.

> Username: Mike-Devel  
> Created_at: 2019-01-24 09:26:33 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250521993  

> I think, the point here is that it's better to avoid manual enumeration of the headers and generate it based on the filesystem.  
> [...]  
> Or you could simply generate the entire .cpp file from CMake.  
  
That can certainly be done. I think the fundamental question I have first is: Do you guys want the files under ` test/test_cmake` to be part of this PR at all?

> Username: Mike-Devel  
> Created_at: 2019-02-05 19:44:52 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r254018820  

@jeking3: So what is your opinion on this particular issue?


---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2019-01-24 08:58:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpl/pull/40#pullrequestreview-195912495  

📁 test/test_cmake/main.cpp

```diff
 190 |+ int main() {
 191 |+ 
 192 |+ }
```

> Username: Lastique  
> Created_at: 2019-01-24 08:58:31 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250512572  

C++ files must end with a newline.

> Username: Mike-Devel  
> Created_at: 2019-01-24 09:16:01 UTC  
> Updated_at: 2019-02-12 18:18:38 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#discussion_r250518244  

~~Fill fix~~ Done


---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2019-02-12 18:20:41 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-462874561  

Ok, I removed the self-test code. Without being hooked up to travis it is anyway not that useful and it makes the whole thing much more complicated than necessary

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-02-12 18:31:13 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-462878373  

I still see a static dependency list in the PR.  I can merge it, but it's asking for trouble...

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2019-02-13 20:01:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-463346499  

And as I said, I believe that is a good thing and not a bad one. Especially for a library like the MPL that effectively doesn't change anymore.  
  
We can talk about tools/scripts that could generate that list (e.g. boostdep), but at the end of the day, it should still be checked into git and not generated by the user.   
  
Personally I think even that would be a mistake,  but if that is the price for getting cmake support into mpl and moving things forward - so be it.  
  
TDLR: I'd really prefer, if you merge the PR as is, but if that is not acceptable, please tell me what exactly would be.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2019-03-02 16:22:05 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-468935088  

@jeking3:  
Thank you very much. Appologies if my last comment did  sound a bit aggressive. I fear that some unrelated real life stress interfered there. If any problems pop up don't hesitate to ping me.

---

## Comment 12

> Username: jeking3  
> Created_at: 2019-03-05 18:49:53 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-469811349  

My take is that when you have a dependency analysis tool that can already do this reliably, you should use that instead.  A static list of dependencies will become stale and cause problems down the road.  A static list of dependencies may make sense on release boundaries, with information fed by the dependency scanner.  That's how CMake does it today in their FindBoost script, but if they get it wrong they have to release a new CMake to fix it.  What we don't want to get into is having to release a new Boost to fix a cmake dependency list being stale or wrong because someone changed some code.    
  
So either we have static library dependency lists AND tests that ensure they are correct, or we use dynamically generated dependency lists that will be as correct as the generator.

---

## Comment 13

> Username: Mike-Devel  
> Created_at: 2019-03-05 19:12:36 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-469819462  

Please clarify what you mean by dynamically generated. Created by the user or by the library maintainer?

---

## Comment 14

> Username: jeking3  
> Created_at: 2019-03-06 00:08:44 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-469911172  

Ideally as part of the build process of the library.  So if I do "b2" in my library the cmake file gets updated with the correct immediate dependencies.

---

## Comment 15

> Username: Mike-Devel  
> Created_at: 2019-03-06 07:15:18 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-469995940  

Again: user or developer? If the user had to run b2 which first would have to compile boostdep in order to use cmake that would be a usability nightmare and largely defeat the purpose.

---

## Comment 16

> Username: Mike-Devel  
> Created_at: 2019-03-07 13:12:45 UTC  
> Updated_at: 2019-03-07 13:13:28 UTC  
> Url: https://github.com/boostorg/mpl/pull/40#issuecomment-470519838  

One thing to mention w.r.t. to testing: When called from a file like this: https://github.com/boostorg/boost/pull/193 it would be fairly trivial to compile (almost all) unit-tests of boost-mpl: https://github.com/Mike-Devel/mpl/commit/cd786f0ed9c01d296e4693e05abd1c18d779ad7e. That should give some confidence that the list of dependencies is in fact correct.

---
