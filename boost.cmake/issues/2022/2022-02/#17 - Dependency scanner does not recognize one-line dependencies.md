# #17 - Dependency scanner does not recognize one-line dependencies [Closed]

> Username: Lastique  
> Created at: 2022-02-02 11:42:25 UTC  
> Updated at: 2025-09-18 16:11:31 UTC  
> Closed at: 2025-09-18 16:11:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/17  

Originally reported in https://github.com/boostorg/filesystem/issues/226.  
  
If `CMakeLists.txt` of a library contains one-line dependency such as this:  
  
```  
target_link_libraries(boost_filesystem PRIVATE Boost::atomic)  
```  
  
the dependency scanner does not recognize this. This affects Boost.Filesystem, so issuing this command on Boost root:  
  
```  
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DBOOST_INCLUDE_LIBRARIES=gil -DBUILD_SHARED_LIBS=ON -DBUILD_TESTING=ON -DBoost_VERBOSE=ON ..  
```  
  
results in this error:  
  
```  
CMake Error: install(EXPORT "boost_filesystem-targets" ...) includes target "boost_filesystem" which requires target "boost_atomic" that is not in any export set.  
```  
  
The dependency on Boost.Atomic is [here](https://github.com/boostorg/filesystem/blob/41d076ace558cfae01e233f9746dc955dcf78dba/CMakeLists.txt#L197).

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-02-02 15:29:17 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028058047  

I'll look into this. Got an idea already.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-02-02 16:08:23 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028100406  

The dependency scanner at the moment only recognizes "Boost::foo" in a line of its own, it doesn't contain a full CMake parser.

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-02-02 16:39:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028130071  

Checked the relevant sources of the includer/scanner. My approach would change it quite a lot, so I want to put it up for discussion first:  
- Generate a list of libraries to add based on `BOOST_EXCLUDE_LIBRARIES`, `BOOST_ENABLE_MPI`, `BOOST_ENABLE_PYTHON` and `BOOST_INCLUDE_LIBRARIES`, basically what is done (minus the deps) in https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L256  
- For each of those call `add_subdirectory` and add them to a "handled" list  
- Add all to a list of libraries to scan (for dependencies)  
- while this list isn't empty, pop the first and do the following:  
    - Get the correct target using a heuristic, e.g. https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L146  
    - Get the `LINK_LIBRARIES` property of this target and combine with `INTERFACE_LINK_LIBRARIES` skipping duplicates  
    - loop over this list and search for entries prefixed `Boost::` that are not in the "handled" list  
    - include the libraries CML  
    - add it to the handled and to-scan lists  
  
I'm sure it works for all libraries where it currently works and now also handles cases as in filesystem. It requires authors to follow a naming convention so we can convert between path-name and target name, but at least for the auto-installation to work this is already the case (although a soft failure, which I'd make a hard failure as otherwise we'll miss dependencies): https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L146  
  
However the current scanner includes(supposed) dependencies that are conditional, i.e. atomic, if formatted correctly: https://github.com/boostorg/filesystem/blob/41d076ace558cfae01e233f9746dc955dcf78dba/CMakeLists.txt#L195-L198  
  
The described approach would only include atomic if it is actually added as a dependency, i.e. earlier than C++20-mode. I'd see this as a bonus.  
  
Any comments? Possible issues?  
  
Quick solution: Enhance the heuristic at https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L176 to allow cases as in filesystem. However that may break again, e.g. for multiple deps on one line.  
The above described solution uses CMake to handle the deps, so all valid CMake constructs will be recognized and conditional dependencies only included, when actually added. That might cause issues too, i.e. if someone adds/links `Boost::filesystem` and assumes `Boost::atomic` to be available even in C++20 mode which due to the `if`-condition above won't be with the new algorithm.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-02-02 16:57:33 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028147536  

That's possible and I've had this in mind as an alternative, but it has its own issues, although on balance it may still be better than what we have now.  
  
It will change the order of add_subdirectory calls, but only sometimes, so order dependence will sometimes work and sometimes not. It also doesn't handle libraries that aren't named "correctly", such as Test and Stacktrace, but maintaining an exception list for those is probably more manageable than fixing the scanner to support whatever people write.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-02-02 17:17:22 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028167241  

Oh, it also breaks one interesting case that currently works and is in use: setting BOOST_INCLUDE_LIBRARIES and BOOST_EXCLUDE_LIBRARIES to the same value of e.g. `lib`. This means, include the dependencies of `lib`, but not `lib` itself, which can't be done with the above approach.

---

## Comment 6

> Username: Flamefire  
> Created at: 2022-02-02 18:28:25 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028231887  

> It will change the order of add_subdirectory calls, but only sometimes, so order dependence will sometimes work and sometimes not.  
  
I think CMLs of independent libs relying on order are ill-formed. Any dependency between 2 (boost) libs more than `target_link_libraries` should be changed, if there are such cases. And if not the include-dependency shouldn't matter. Am I missing anything here?  
  
> Oh, it also breaks one interesting case that currently works and is in use: setting BOOST_INCLUDE_LIBRARIES and BOOST_EXCLUDE_LIBRARIES to the same value of e.g. `lib`. This means, include the dependencies of `lib`, but not `lib` itself, which can't be done with the above approach.  
  
Hm, interesting indeed. Where is this used? However I think we could say that this is be undefined behavior and worked by accident ;-) I mean: What is a system supposed to do when told to do opposites?  
Anyway: Indeed this cannot be supported by the "new" algorithm as in order to get the dependencies you need to create the target so you can check it. Best we can use is to add a custom exclude-from-all list, which gets close to that behavior.  
  
As a slight alternative: How about using:  
```  
set(boost_regex "Boost::([A-Za-z0-9_]+)")  
file(STRINGS "${BOOST_SUPERPROJECT_SOURCE_DIR}/libs/${lib}/CMakeLists.txt" data REGEX .*${boost_regex}.*)  
string(REGEX MATCHALL ${boost_regex} deps ${data})  
```  
And then do the loop at https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L174-L183 over `${deps}` but replace the regex by the above variable.  
I.e. search the whole file for possible boost libs anywhere.  
  
Again conditional dependencies will be always included but the only requirement would be to not use a variable (e.g. `link_libraries(Boost::${thisOrThat})`)  
  
I think I still prefer the CMake-Target-approach as it does not introduce any kind of subdialect library-authors would need to follow and any valid CMake suffices.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-02-02 18:33:30 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028236632  

> Hm, interesting indeed. Where is this used?  
  
Here: https://github.com/boostorg/json/blob/21fcc880304c754aeb01421318e06563484c72ac/CMakeLists.txt#L42-L53

---

## Comment 8

> Username: pdimov  
> Created at: 2022-02-02 18:35:36 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028238186  

> As a slight alternative: How about using  
  
I fear that this will pick up more than we need, although it's an option if all else fails.

---

## Comment 9

> Username: Flamefire  
> Created at: 2022-02-02 18:48:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028248074  

> Here: https://github.com/boostorg/json/blob/21fcc880304c754aeb01421318e06563484c72ac/CMakeLists.txt#L42-L53  
  
Nifty! I'm actually surprised this works... Couldn't that still be done by putting that at the top without the exclude, guarding the if against re-entry and returning after including the super-project CML? Basically abuse that file(-part) as a launcher script for the super-project build.  
  
> I fear that this will pick up more than we need, although it's an option if all else fails.  
  
For example? We filter already against the existing libs via the paths.  
Ok comments could be a problem...

---

## Comment 10

> Username: Lastique  
> Created at: 2022-02-02 19:23:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028279838  

I do like the idea of using CMake to discover all dependencies. This solution is future-proof, too.  
  
> As a slight alternative: How about...  
  
I think, if the scanner has to parse CMakeLists.txt by itself, it should be more robust than that. I.e. it should recognize `link_libraries`/`target_link_libraries` (non-commented and written in any valid way) and parse its arguments rather than picking random mentions of `Boost::` strings, whether they are actual targets or comments or strings or whatever else. Although even recognizing `target_link_libraries` isn't perfect, as that won't work when the target name(s) are in a variable and would not reflect conditional dependencies. Still, that would be better than the current implementation.

---

## Comment 11

> Username: pdimov  
> Created at: 2022-02-02 19:46:29 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028297504  

I'm not particularly thrilled by the prospect of having to perform proper tokenization and maintain state across lines in order to parse every possible `target_link_libraries` call. Besides, the current approach actually works for more than that, e.g. https://github.com/boostorg/test/blob/01ffbe2521bbbc8fa646e9a4fbf68a5c89241de0/CMakeLists.txt#L11-L30

---

## Comment 12

> Username: Flamefire  
> Created at: 2022-02-02 20:57:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028351407  

Yeah, either we simply use the enhanced regex matcher and maybe filter comment lines or do the "proper" approach with targets. Writing a cmake parser in cmake is to much.

---

## Comment 13

> Username: mloskot  
> Created at: 2022-02-02 21:12:44 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028362620  

The conflict between maintainer hand rolling free-form CML and strict-form expectations from  Boost CMake is inevitable.  
  
A not entirely crazy idea: split the two sets of CML-s into mandatory generated CMakeLists.boost.txt and optional manually maintained by a lib maintainer CMakeLists.txt

---

## Comment 14

> Username: Lastique  
> Created at: 2022-02-02 21:30:34 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028376042  

> A not entirely crazy idea: split the two sets of CML-s into mandatory generated CMakeLists.boost.txt and optional manually maintained by a lib maintainer CMakeLists.txt  
  
I'm not sure how that would work with non-trivial dependency selection. Such as what Boost.Filesystem does.

---

## Comment 15

> Username: mloskot  
> Created at: 2022-02-02 21:58:38 UTC  
> Updated at: 2022-02-02 22:00:32 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028397247  

> > A not entirely crazy idea: split the two sets of CML-s into mandatory generated CMakeLists.boost.txt and optional manually maintained by a lib maintainer CMakeLists.txt  
>   
> I'm not sure how that would work with non-trivial dependency selection. Such as what Boost.Filesystem does.  
>   
  
  
I assumed the CMakeLists.boost.txt is generated with `boostdep --cmake <lib>` hence it's complete, correct and well-formed for the dependency scanner and other Boost tools parsing it.   
  
The CMakeLists.txt is what maintainer(s) desires for their development and testing workflows. It's it used by any of Boost CMake part.  
  
Like in pre Boost CMake days, every lib had Jamfile-s, some libs had CMakeLists.txt, and a lib maintainers and users relied on both for own purposes.  
  
Let's treat CMakeLists.boost.txt as a new third actor on the stage.

---

## Comment 16

> Username: Lastique  
> Created at: 2022-02-02 22:10:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028405455  

`boostdep` does not generate the correct CMakeLists.txt for all libraries. And besides, I wasn't a fan of having to support two build systems, and I'm even less of a fan of having to maintain three.

---

## Comment 17

> Username: mloskot  
> Created at: 2022-02-02 22:32:24 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028420801  

> `boostdep` does not generate the correct CMakeLists.txt for all libraries.  
  
I wasn't aware of that.   
  
> And besides, I wasn't a fan of having to support two build systems, and I'm even less of a fan of having to maintain three.  
>   
  
I assume there's practically no maintenance of Boost CMake configuration required - it's all generated.   
  
However, now there seem to be two (modes of) configurations in single CMakeLists.txt file which I find troublesome - I enjoyed the ability to call my CMakeLists.txt an unofficial configuration and that I could stick any rubbish in it I desired without worrying I could break anything Boost-wide.

---

## Comment 18

> Username: Flamefire  
> Created at: 2022-02-03 10:10:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028826329  

> The conflict between maintainer hand rolling free-form CML and strict-form expectations from Boost CMake is inevitable.  
>   
> A not entirely crazy idea: split the two sets of CML-s into mandatory generated CMakeLists.boost.txt and optional manually maintained by a lib maintainer CMakeLists.txt  
  
Well the limitation to put dependencies onto own lines is a very minor one IMO. A fix for the issue as it came up here is very easy if one knows that. We could even improve the scanner to output potential but ignored dependencies to provide more guidance to library authors.  
  
I also agree that having to maintain 3 build systems is to much. Combining the Boost.CMake and the (more or less) standalone (or better: top-level-build) CML is OK as the difference is quite minor.  
Besides that: The file has to be named "CMakeLists.txt", not "CMakeLists.boost.txt" for CMake `add_subdirectory` to work, so that is not even an option besides that I think what `boostdep` outputs is only a starting point for a proper CML.  
  
> I enjoyed the ability to call my CMakeLists.txt an unofficial configuration and that I could stick any rubbish in it I desired without worrying I could break anything Boost-wide.  
  
You can still do that. Maybe simply put a `if(DEFINED BOOST_SUPERPROJECT_VERSION) message(FATAL_ERROR "Not supported") endif()` at the top to opt-out. ;-) Of course better would be, if you really want, to have this `if`-condition with an else branch and have the (possibly enhanced) output of `boostdep` in the first part and your "unofficial" custom stuff in the else branch. So basically your 2-file approach but in 1 file separated by that branch.  
  
> > I'm not sure how that would work with non-trivial dependency selection. Such as what Boost.Filesystem does.  
>   
> I assumed the CMakeLists.boost.txt is generated with `boostdep --cmake <lib>` hence it's complete, correct and well-formed for the dependency scanner and other Boost tools parsing it.  
  
Just as a note: This is impossible for `boostdep` to achieve as those kinds of dependency selections are called "non-trivial" for a reason ;-) We'd basically need a B2 parser in there, detect feature-detection-patterns and likely run into similar, but harder to solve issues as the the current CMake "parsing"

---

## Comment 19

> Username: mloskot  
> Created at: 2022-02-03 11:11:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1028875702  

> > I enjoyed the ability to call my CMakeLists.txt an unofficial configuration and that I could stick any rubbish in it I desired without worrying I could break anything Boost-wide.  
>   
> You can still do that. Maybe simply put a `if(DEFINED BOOST_SUPERPROJECT_VERSION) message(FATAL_ERROR "Not supported") endif()` at the top to opt-out. ;-) Of course better would be, if you really want, to have this `if`-condition with an else branch and have the (possibly enhanced) output of `boostdep` in the first part and your "unofficial" custom stuff in the else branch. So basically your 2-file approach but in 1 file separated by that branch.  
  
Yes, that's what GIL's CML does.

---

## Comment 20

> Username: pdimov  
> Created at: 2022-02-03 17:19:32 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029218442  

> I enjoyed the ability to call my CMakeLists.txt an unofficial configuration and that I could stick any rubbish in it I desired without worrying I could break anything Boost-wide.  
  
That is why I made the dependency scanner stupid on purpose, to minimize the probability of it picking up things in your rubbish part. But since it appears Andrey can't be arsed to press Enter twice, we'll need to figure something out.

---

## Comment 21

> Username: Flamefire  
> Created at: 2022-02-03 17:29:32 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029227754  

We simply have to decide for one solution. Fastest would be the current parser, with the following change:  
  
```  
set(boost_regex "Boost::([A-Za-z0-9_]+)")  
file(STRINGS "${BOOST_SUPERPROJECT_SOURCE_DIR}/libs/${lib}/CMakeLists.txt" data REGEX [^#]*${boost_regex}.*)  
string(REGEX MATCHALL ${boost_regex} deps ${data})  
```  
  
I'm pretty sure this would work and due to the first regex even exclude comments. Well until someone does  
  
```  
target_link_libraries(foo  
    Boost::bar # Boost::baz  
)  
```  
  
If no one does that currently no library needs changes now. With the CMake-based scanner at least Boost.Json needs to change. With the current scanner Boost.Filesystem needs to change.  
  
So choose your poison I guess. Maybe circulate this on the list?

---

## Comment 22

> Username: pdimov  
> Created at: 2022-02-03 17:42:50 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029239278  

No, this will pick up much more than I'd have liked. I think I'll just hardcode Andrey's line as-is.

---

## Comment 23

> Username: Lastique  
> Created at: 2022-02-03 19:48:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029341708  

> But since it appears Andrey can't be arsed to press Enter twice  
  
Ouch!  
  
Yes, I can add newlines, and I probably will if I have to before the next release. But I think the current parser needs to be fixed as well, hence this issue. It's not just my spite, it is simply reasonable to expect a valid CMakeLists.txt to work.  
  
BTW, this whole issue was the first time I ever heard that there is some sort of a parser at all. I was writing CMakeLists.txt on the premise that CMake is the only tool that will interpret it. I have a hard time believing that I'm the only one ignorant like that.  
  
> I think I'll just hardcode Andrey's line as-is.  
  
If you're going to add something in the spirit of "if Filesystem then add Atomic dependency" then I'd rather you didn't.

---

## Comment 24

> Username: pdimov  
> Created at: 2022-02-03 20:00:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029350716  

Yeah, I can't in good conscience hold others responsible for not reading the document I'm writing for more than a year now and haven't yet published. :-/  
  
But in short, the dependency parser doesn't have anything to do with the CMake correctness of the CML files, they don't depend on it in order to work, it's only engaged when the user does `cmake -DBOOST_INCLUDE_LIBRARIES=mylib`. Since it's awfully user-hostile to make him include manually all libraries that `mylib` needs as dependencies (that's how it used to work and it wasn't pretty), the root CML needs to somehow figure out how to `add_subdirectory` the dependencies of `mylib`.  
  
And the problem is that CMake is very picky when there's install support, and mandates that the dependencies are 100% correct even if one doesn't install anything.  
  
It's obviously not possible to somehow parse an arbitrary CML file with a parser written in CMake - that's madness. And making the scanner pick up anything resembling a dependency isn't good either, because people have all sorts of things in their CML files that are only active when the library is used as a root project.  
  
So as a compromise I've settled on only picking up a dependency if it's alone on a line. This allows the portion of the CML that needs to be scanned to be written in a way such that the scanner picks up exactly what one wants, and no more.

---

## Comment 25

> Username: Lastique  
> Created at: 2022-02-03 20:34:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1029377498  

So, what's it going to be? Is the scanner going to employ the target-based approach? Because if your plan is to hardcode Filesystem I'd rather add newlines (with a promise to break it from time to time ;) ).

---

## Comment 26

> Username: Lastique  
> Created at: 2022-02-17 16:01:01 UTC  
> Url: https://github.com/boostorg/cmake/issues/17#issuecomment-1043119440  

> So, what's it going to be? Is the scanner going to employ the target-based approach? Because if your plan is to hardcode Filesystem I'd rather add newlines (with a promise to break it from time to time ;) ).  
  
Ping?
