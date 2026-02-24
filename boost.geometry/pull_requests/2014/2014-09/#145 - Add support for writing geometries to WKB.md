# #145 Add support for writing geometries to WKB [Merged]

> Username: meastp  
> Created at: 2014-09-30 13:17:08 UTC  
> Updated at: 2015-04-24 10:28:08 UTC  
> Merged at: 2015-04-23 15:21:46 UTC  
> Closed at: 2015-04-23 15:21:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/145  

This patchset adds support for writing to the WKB format from geometries. There are a couple of fixes to support geometry adaptions there as well, and I had to modify the geometry_type to support different dimensions and formats.  
  
This implementation is generic enough to be able to support CIRCULARSTRING, implemented on top of this implentation locally, so it should be sufficiently user-extendable for some use cases.  
  
Current limitations:   
- endian conversion is not supported (yet)  
- there might be some use of C++11 in there, which I will remove when the direction of this PR  
- no support for multi-geometries (yet)  
- there might be traces of CIRCULARSTRING left.  
  
I am prepared to do more work to get this patch into Boost.Geometry proper, as it is a hassle to maintain this out-of-tree. :)

---

## Comment 1

> Username: mloskot  
> Created_at: 2014-09-30 13:26:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57312816  

@meastp I remember, I had promised to incorporate your original work done against the old SVN trunk, but I failed due to complete lack of time. I'm very thankful you're submitting this to Git repo, hopefully others will agree to merge it soon.

---

## Comment 2

> Username: meastp  
> Created_at: 2014-09-30 13:35:06 UTC  
> Updated_at: 2014-10-01 11:20:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57313980  

@mloskot I'm not sure if the MULTI-geometry patches I submitted a while ago, are 100% compatible with this patch. I think the modification that has to be done is minor, and it should be merged after writing WKB  is merged, IMHO.

---

## Comment 3

> Username: mloskot  
> Created_at: 2014-09-30 13:59:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57317586  

@meastp I see. Hmm, I think I will have to get on this. I may have a moment next week.

---

## Comment 4

> Username: meastp  
> Created_at: 2014-10-01 06:55:39 UTC  
> Updated_at: 2014-10-01 06:57:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57425857  

@awulkiew Ok, I have rebased the PR, removed the most [controversial commit](https://github.com/Norkart/geometry/commit/ef8a3a404b54e41c752d49e61ea11a163f1d40ad#commitcomment-7981772) :), and made sure it compiles locally (after the rebase, I can't see the commit+comment in this PR, hence the link).  
  
As long as there is an easy way to fix the  `point_type<>` -issue locally and without forking boost.geometry, it is not important to me how it is done.  
  
(also, please tell me if rebasing is not ok when working on a PR)

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-10-01 08:59:15 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r18267533  

Why is this new code commented?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-10-01 08:59:58 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r18267558  

Why commented?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-10-01 09:04:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57437002  

Hi Mats, thanks for your work!  
Can you eloborate a bit on this:   
_As long as there is an easy way to fix the point_type<> -issue locally and without forking boost.geometry, it is not important to me how it is done._  
What is the issue?  
We don't use c++11 dependancies (unless ifdeffed), can they be removed?

---

## Comment 8

> Username: meastp  
> Created_at: 2014-10-01 10:17:59 UTC  
> Updated_at: 2014-10-01 10:26:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57444284  

@barendgehrels I did this work a while ago, so unfortunately I do not remember the exact details. However, it is important for me as a user to be able to adapt custom geometry types and use them with no changes in Boost.Geometry proper.  
  
But, let us focus on the WKB patch first. It should work without changes to `point_type` for most geometry types. :)  
  
I will remove the C++11 stuff, it should only be use of _auto_ anyway, and almost trivial to fix. :)

---

## Comment 9

> Username: meastp  
> Created_at: 2014-10-01 10:24:40 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r18270533  

Unfortunately, I do not have access to a machine with different endianness, so I have not tested it properly. Conversion between endianness needs to be tested.

---

## Comment 10

> Username: meastp  
> Created_at: 2014-10-01 10:25:21 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r18270561  

Until endianness is handled properly, I decided to wait with the implementation of this function.

---

## Comment 11

> Username: meastp  
> Created_at: 2014-10-01 11:13:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57448993  

I saw the merge window notice for Boost 1.57. It would be lovely if this PR could get into 1.57 (as an extension). I will try to respond quickly to any feedback. :)

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2014-10-01 13:01:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-57459194  

Mats: OK about focus first. However, extensions are not part of any release, so 1.57 is not relevant here...

---

## Comment 13

> Username: mloskot  
> Created_at: 2014-11-05 09:44:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-61782357  

If no objections, I'd like to take care of testing and merging this one.  
(after that, I'm going to update and merge read support for MULTI WKB from https://svn.boost.org/trac/boost/ticket/9066).  
  
As far as I can see, all issues during the initial reviews have been addressed, so it looks it's ready to merge, isn't it?  
  
I believe, I have means to test it on big-endian hardware. Then, I'd like to merge it. Does anyone agree?

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-11-05 10:26:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-61787098  

Sure, please go ahead with testing and merging, it would be great if WKB will end up in the releases, so moved from the extensions

---

## Comment 15

> Username: meastp  
> Created_at: 2015-02-20 11:07:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-75222109  

Is this merged yet? If not, is there anything I can do to accelerate the process? :)

---

## Comment 16

> Username: awulkiew  
> Created_at: 2015-02-25 16:28:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-75992719  

AFAIU the only reason why this wasn't merged yet is that @mloskot wanted to do it. I think there are no objections. @mloskot should we merge it or wait?

---

## Comment 17

> Username: mloskot  
> Created_at: 2015-02-25 16:33:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-75993885  

I was going to test it against big-endian arch, but I haven't managed to dig for my QEMU environments. Sorry, no time really. If you want to go ahead and merege, no objections of course. Thanks.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2015-02-25 16:37:47 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r25354918  

No newline at end of file

---

## Comment 19

> Username: awulkiew  
> Created_at: 2015-02-25 16:38:01 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r25354939  

No newline at end of file.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2015-02-25 16:41:25 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r25355264  

Why those test cases was commented out? Is there a regression?

---

## Comment 21

> Username: awulkiew  
> Created_at: 2015-02-25 16:49:03 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r25356025  

This and 133 lines are probably too long. In such cases we put the for-loop expressions or template parameters in separate lines, e.g.:  
  
```  
for(boost::range_iterator<const L>::type point_iter = boost::begin(linestring);  
    point_iter != boost::end(linestring);  
    ++point_iter)  
{  
    writer_assigner  
        <  
            point_type, 0, dimension<point_type>::value  
        >::run(*point_iter, iter, byte_order);  
}  
```

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-02-25 16:53:21 UTC  
> Updated_at: 2015-04-23 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#discussion_r25356447  

Here similar case, for-loop (empty line and big indentation) and template parameters of `writer_assigner`.  
  
Btw there are some guidelines for developers, here: https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers

---

## Comment 23

> Username: meastp  
> Created_at: 2015-03-13 14:27:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-78995740  

Thank you, I will look at this and fix as soon as possible :)

---

## Comment 24

> Username: meastp  
> Created_at: 2015-04-17 17:34:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-94034204  

@awulkiew : I have tried to fix all the violations of the guidelines I could find.  
  
The other comment, about regressions in reading WKB (https://github.com/boostorg/geometry/pull/145#discussion-diff-25355264L25) :  
  
The (removed) tests verified that one could e.g. read a PostGIS EWKB 3D point into a boost::geometry 2D point, where the WKB reader would implicitly ignore the last point. The reason I removed these tests was that this (surprising) behaviour will only ever work consistently with points – never with 3D vs 2D linestrings and polygons – and is therefore (imho) unwanted (and surprising) behaviour.  
  
With this patch, 2D and 3D geometries are supported.  
  
The patch has an unfinished implementation for (explicit) file formats (ogc wkb and ewkb in detail/ogc.hpp), but I am hoping this patch can be merged as is, and full file format support completed afterwards, in a separate pull request.

---

## Comment 25

> Username: meastp  
> Created_at: 2015-04-17 17:37:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-94035084  

@awulkiew @mloskot (When the pull request is ready to merge, I (or you, as part of the merge) should probably it to squash the "fixup" commits)

---

## Comment 26

> Username: awulkiew  
> Created_at: 2015-04-17 23:19:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-94095582  

Thanks the the fixes!  
AFAIS there are still some "no newline at the end of file" issues.

---

## Comment 27

> Username: meastp  
> Created_at: 2015-04-18 11:12:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-94156102  

@awulkiew : If I understand correctly, the issue is that there is a newline or carriage return at the end of some files. This contribution was made in Windows and Visual Studio, and I tried to remove the newline, with no luck. I also tried vi, unfortunately without luck.  
  
I need help to be able to fix the newline at end of file issue(s)...

---

## Comment 28

> Username: awulkiew  
> Created_at: 2015-04-22 02:17:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-94999627  

@meastp actually it's the opposite. There is no newline at the end of file but there should be. Some compilers will generate a warning if a file doesn't end with the newline.

---

## Comment 29

> Username: meastp  
> Created_at: 2015-04-22 07:14:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95055666  

@awulkiew : Thanks for explaining the issue. I have fixed this with the online github editor (which is why there is one commit per file). Are there any issues left? :)

---

## Comment 30

> Username: awulkiew  
> Created_at: 2015-04-22 14:02:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95189856  

@meastp There are tabs used for indentation. At Boost we don't use tabs. In particular in Boost.Geometry we use 4 spaces.  
  
Would it be possible to squash the commits somehow? Most preferably grouping the changes made inside logical parts of the library. E.g. changes done in extensions headers and other for tests.  
Or at least squash the commits containing all "guideline" fixes into one.  
  
Also note, that in Boost.Geometry we add some tags in front of the commit message, e.g. in this case the tags could be `[extensions]` or `[extensions][wkb]` and `[extensions][test]`.

---

## Comment 31

> Username: meastp  
> Created_at: 2015-04-22 19:34:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95312072  

I will fix the remaining issues and do a rebase of the entire pull request to fix the commits. :)

---

## Comment 32

> Username: meastp  
> Created_at: 2015-04-23 06:12:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95454027  

@awulkiew I have fixed the remaining issues and squased the commits + rebase. It should be ready to merge :)

---

## Comment 33

> Username: mloskot  
> Created_at: 2015-04-23 08:08:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95485076  

:+1: Big thanks @meastp for the efforts and @awulkiew for taking the integration over

---

## Comment 34

> Username: awulkiew  
> Created_at: 2015-04-23 15:21:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95623032  

Thanks @meastp !  
  
There is one small thing, i.e. instead of manually printing the debug info in  `test_geometry_equals()` `BOOST_CHECK_MESSAGE()` could be used. I'll change this after merging.

---

## Comment 35

> Username: barendgehrels  
> Created_at: 2015-04-23 17:14:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95659357  

Thanks a lot Mats!   
Very glad it is finished and merged now and will be part of Boost 1.59

---

## Comment 36

> Username: awulkiew  
> Created_at: 2015-04-23 20:43:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95713756  

It's in the extensions so it won't be released in 1.59 unless it's moved into the "official" part. Though AFAIU it's not ready.  
  
Btw, there are some GCC compilation errors which I'm going to fix.

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2015-04-23 20:59:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95717288  

Good to know. Why it is in the extensions? As far as I know there are no dependencies on other parts in extensions? What part is not ready (besides the fixing - thanks for that)?

---

## Comment 38

> Username: awulkiew  
> Created_at: 2015-04-23 21:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95726329  

Mats, please check out the fix and say if it's ok: https://github.com/boostorg/geometry/pull/282 e.g. if it doesn't interfere with your plans.

---

## Comment 39

> Username: meastp  
> Created_at: 2015-04-24 05:33:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/145#issuecomment-95806170  

@awulkiew : Great! That's a better way to do it, and it does not interfere with the long(er) term goal of supporting multiple wkb formats (ogc, ewkb, ogc-sqlmm etc...). :)

---
