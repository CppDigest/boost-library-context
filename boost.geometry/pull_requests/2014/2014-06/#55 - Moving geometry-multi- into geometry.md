# #55 Moving geometry/multi/ into geometry/ [Merged]

> Username: awulkiew  
> Created at: 2014-06-03 19:38:03 UTC  
> Updated at: 2014-06-21 10:32:30 UTC  
> Merged at: 2014-06-05 14:16:08 UTC  
> Closed at: 2014-06-05 14:16:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/55  

With this small chunk of changes I'd like to start the process of (re)moving the multi directory.  
Do we still agree that this should be done?  
  
This first test commit moves the core. It should be simple to move other parts too (maybe besides the algorithms) so I'll probably add them to this PR. But I think it would be better to merge smaller chunks to see if everything is ok on all platforms.  
I also tweaked the headers in some files to include only the required functionalities of TypeTraits and Range.  
  
For backward-compatibility the original multi files #include only the non-multi header. In the future we can put some #warnings in there to inform about the deprecation and later remove the directory.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-06-03 20:02:47 UTC  
> Updated_at: 2014-06-05 13:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13355846  

Could you please check that the above white space is not a tab?  
Same for the few lines below.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-03 20:04:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45013251  

As part of this pull request I would also expect to have all includes that contain "multi/core" replaced by the corresponding ones that do not have "multi" in the path.

---

## Comment 3

> Username: ahundt  
> Created_at: 2014-06-03 20:21:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45015111  

I concur with moving the multi directory, when I was searching for multipolygon I looked in the geometries directory and didn't see it so I ended up doing a search. When I found out where the files were I thought it was unexpected, so I think ditching the multi directory is a good idea.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-06-03 20:22:25 UTC  
> Updated_at: 2014-06-05 13:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13356824  

Those are spaces. I moved those base types to allign them with the ones for specializations for longer tag names.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-06-03 20:23:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45015383  

We agreed that it should be done AFTER release.  
Please postpone this.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-06-03 20:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45016880  

@mkaravel I believe all files including headers from multi/ directory resides in multi/ directory or are tests. In the case of those from multi directory they will be modified to only include the non-multi version in the process. In the case of tests, I planed to remove those includes when I finish with the headers, but sure all multi/core includes could be removed now.  
  
@barendgehrels Ah, ok. I guess the PR may wait here until then. I don't expect much will change in the parts already modified.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-06-03 20:46:12 UTC  
> Updated_at: 2014-06-03 21:01:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45018041  

Take a look at algorithms/not_implemented.hpp. It includes multi/core/tags.hpp.  
And there are more (but not too many).

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-06-03 21:23:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45022527  

@mkaravel Ok, that's unexpected. Another reason why this should be done. Thanks for pointing this out. I'll of course remove all of them.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2014-06-04 08:58:17 UTC  
> Updated_at: 2014-06-04 09:01:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45067235  

@awulkiew Seeing all the daily changes in Boost.Core, we will have some time. So in retrospective I think we can do this PR now, because its scope is limited. So I'm OK with this.  
  
In case you plan further work on this:  
Please don't touch multi/algorithms yet, this is far more work because we want to divide it into the new (still not fully definitive) structure too. We can do that after 1.56 is out. Also please don't touch multi/geometries for backward compatibility, users probably include this directly.   
_EDIT_ oh I notice you did this (geometries) too... Well, I'm OK with that too iff you add a small header file there for backward compatibility, including the new place.  
  
For the rest (views, io, strategies) I'm OK with moving them.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-06-04 11:20:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45078829  

@barendgehrels Ok, I propose to move some details of algorithms too (for_each_range, point_in_geometry, etc.). I believe that they can be moved without introducing the new structure.  
  
All files in multi/ directory just include the corresponding files from geometry/ directory. So we are backward compatible, all tests passes as they are now including headers from multi/ directory. However I also plan to remove all includes of moved parts.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-06-04 12:40:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45085061  

@awulkiew OK - some details is fine as long as it has no wider consequences, I don't want to hold you back ;-)   
About your "I also plan to remove all includes" - do you mean those of geometries too? I don't think that is possible, they are really used by end-users. They should be there at least for one release. Maybe we should add a _#warning_ there

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-06-04 12:56:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45086470  

@barendgehrels Of course, those files should be there, at least for now. I had in mind removing/modifying #include preprocessor directives when possible, like in the commit above.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2014-06-04 15:14:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45103561  

In order to move for_each_range I refactored it a little bit. As a side effect the TypeOf dependency is dropped. Could you please check if you agree with my changes?

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-06-04 15:58:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45109936  

I did have a glance, it looks good. Good add_const... is gone and TypeOf too. Thnks.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2014-06-04 16:53:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45117347  

Ok, I think I'll stop changing the includes. Everything is moved instead of the algorithms.  
  
As a part of this PR I'd like to update the extensions, docutils and examples.  
I'm not sure about the tests, we should probably test for backward compatibility.  
What do you think?

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-06-04 18:11:48 UTC  
> Updated_at: 2014-06-05 13:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13401469  

I modified those to be backward compatible but shouldn't geometries be removed from here? This file is included in geometry.hpp and as it's described in docs BG geometries implementations shouldn't be included by default.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2014-06-05 08:03:09 UTC  
> Updated_at: 2014-06-05 13:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13427893  

@awulkiew My personal point of view here is that:  
1) all headers in this file should be included in geometry.hpp (you probably do that already; I did not check)  
2) inclusion of multi.hpp should be removed in geometry.hpp  
3) multi.hpp should only include geometry.hpp  
4) potentially add a warning that this file is deprecated (or something similar)

---

## Comment 18

> Username: mkaravel  
> Created_at: 2014-06-05 08:06:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45191830  

@awulkiew I saw that files in multi/*/detail currently include the corresponding version in */detail.  
  
From my point of view these files should be completely eliminated: they are not part of the public interface, in the library code they should no longer be used, and I do not see the point of keeping them (as they are not part of the public interface, there is no backward compatibility requirement, at least in theory).

---

## Comment 19

> Username: awulkiew  
> Created_at: 2014-06-05 11:08:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#issuecomment-45206752  

@mkaravel I agree with you, however some tests would be broken and I'm considering leaving them untouched for now.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2014-06-05 11:12:36 UTC  
> Updated_at: 2014-06-05 13:24:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13434238  

@mkaravel I agree with you but multi/multi.hpp is included in some tests and we should avoid including the whole geometry.hpp in the tests.  
Of course the above has sense if we want to keep the tests untouched for now.  
  
But what about the geometries?

---

## Comment 21

> Username: awulkiew  
> Created_at: 2014-06-05 13:27:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/55#discussion_r13438872  

I was mistaken here. Clearly those are the concepts, not models. And the concepts are now included in concepts/check.hpp with the rest of the concepts so no need to include them here.  
  
Sorry for confusion!

---
