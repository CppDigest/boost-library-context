# #121 Add template of Minimal Working Example program [ci skip] [Closed]

> Username: mloskot  
> Created at: 2018-07-25 20:43:22 UTC  
> Updated at: 2018-07-27 10:35:15 UTC  
> Closed at: 2018-07-27 10:34:53 UTC  
> Url: https://github.com/boostorg/gil/pull/121  

### Description  
  
For users and contributors convenience, CMake configuration  
generates basic example, ready to compile.  
  
Apply minor fixes to some CMakeLists.txt files.  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
  
-----  
  
@stefanseefeld This is not a high priority but I think it would be good to let it released with Boost 1.68

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2018-07-25 20:50:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/121#pullrequestreview-140494598  

Why do you add this as a `.cpp.in` file ? Why not a `.cpp` directly ? I don't see anything in the file that's generated.  
  
Also, for an example I would expect some documentation (either inline or as part of the tutorial). And I'd expect it to be compilable with more than just CMake.  
(Of course, all of this can still be added, but I think it should be part of the PR. So I don't think this is ready to be merged just yet.)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-07-25 21:07:53 UTC  
> Updated_at: 2018-07-25 21:09:45 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-407896097  

> Why do you add this as a .cpp.in file ? Why not a .cpp directly ? I don't see anything in the file that's generated.  
  
This file is supposed to be edited/modified by a user/contributor.  
There is no content to be generated.  
If this file was in repository as regular .cpp and if one modifies it then it would lead to dirty status of GIL clone. Using this `.cpp.in` trick, keeps the example out of git scope.  
  
I don't think there is need for documentation - the .cpp.in file could be empty, I just added some sample code for illustration.  
  
I agree, it should be added to Jamfile. It could also be added to Faber, if I knew how to copy a file.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-07-25 21:20:48 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-407899838  

Ah. In that case I would just add it as-is (as a file that does almost nothing but compiles and runs without errors), documenting in the tutorial how this could be modified incrementally (i.e., step-by-step) to do various things.  
I agree such an example would be valuable, but I also think that putting the documentation together that provides enough context / meaning to make this really useful is likely out of scope for the upcoming release.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-07-25 21:23:32 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-407900523  

I agree, such integrated MWE sample should be well documented, in tutorial as well as `CONTRIBUTING.md`, and this should happen in future :-)

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-07-25 22:22:22 UTC  
> Updated_at: 2018-07-25 22:23:46 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-407915466  

I've just realised, `example/Jamfile` is actually empty and does not build anything  
  
https://github.com/boostorg/gil/blob/ea9fb1581f1e1aac25bdccd2ee707d8378868a21/example/Jamfile#L10-L14  
  
So, I think having the `minimal_working_example.cpp` copied by CMake only for now is fine and relevant BBv2 targets will be added in future (along with update of source code of examples to actually compile).  
What you think, @stefanseefeld ?

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-07-27 10:20:20 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-408377544  

OK, so we want to give users a minimal demo file that he can use as base for exploring GIL.  
In that case, I'd expect the user to copy the source file and compile it using his own build system (which could be b2, cmake, faber, or anything else.  
But there is strictly nothing to do for the Boost.GIL build system, at least not in a `build` step. (There is something to do in the `install` and `package` steps, though. But I still fail to understand why you want to generate the source from a `.in` "template" file. There are many examples of Boost libraries that ship with example files, none of which are generated.  
But all those technical details aside, I think the main task remains to augment the (existing) docs so the example file somehow fits into the narrative of the tutorial. And while I think this would be very useful indeed, I don't think we should rush this into the upcoming release. Remember, we are in post-beta mode where only (critical) bug fixes should be added.

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-07-27 10:34:53 UTC  
> Url: https://github.com/boostorg/gil/pull/121#issuecomment-408380678  

>  I still fail to understand why you want to generate the source from a .in "template" file  
  
Simply, convenience.  
  
This approach works well for plain CMake-based projects: `e.cpp.in` is copied to `e.cpp` during configuration phase, automatically added as target/project to workspaces available in an IDE, ready to fill with user-defined code, ready to compile, run and debug.

---
