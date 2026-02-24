# #55 Release Candidate 2 for Boost 1.58.0 [Merged]

> Username: daminetreg  
> Created at: 2015-02-11 06:51:55 UTC  
> Updated at: 2015-03-09 11:30:43 UTC  
> Merged at: 2015-03-06 04:15:09 UTC  
> Closed at: 2015-03-06 04:15:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/55  

It's again me :smile: and again for the same merge, but this time into master to have them for sure in 1.58.0.  
  
I promise it's the last time I ask to merge these same changes as in #50 and #54.  
  
I'll be highly available on devel and users mailing lists upon release of 1.58 to handle any issues or questions regarding these changes or any related parts.

---

## Comment 1

> Username: Flast  
> Created_at: 2015-02-12 12:36:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-74063885  

@djowel, could you re-generate preprocessed files on develop before merging: relating to #49 ?

---

## Comment 2

> Username: daminetreg  
> Created_at: 2015-02-12 14:45:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-74081648  

I did it for testing, but didn't commit it, I thought this was part of the release process of boost. The problem is I cannot modify this source branch.  
  
The best would be to regenerate after merging no ?

---

## Comment 3

> Username: Flast  
> Created_at: 2015-03-05 06:15:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77312773  

> The best would be to regenerate after merging no ?  
  
Yes, but currently, we need regenerating the files to run Boost.Regression perfectly or marked as failed (e.g. [as_deque / msvc-14.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08f-win2012R2-64on64-boost-bin-v2-libs-fusion-test-as_deque-test-msvc-14-0-debug-threading-multi.html))  
  
How about define `BOOST_FUSION_DONT_USE_PREPROCESSED_FILES` while running regression test.  
  
``` diff  
diff --git a/test/Jamfile b/test/Jamfile  
index e748c45..3dd41e3 100644  
--- a/test/Jamfile  
+++ b/test/Jamfile  
@@ -10,6 +10,7 @@ import testing ;  
  
 project  
     : requirements  
+      <define>BOOST_FUSION_DONT_USE_PREPROCESSED_FILES  
     ;  
  
 {  
```

---

## Comment 4

> Username: Flast  
> Created_at: 2015-03-06 04:13:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77504012  

Master will be closed completely tomorrow: it is a last chance to merge this.  
  
I think those feature changes are well tested and good for 1.58. Any comments?

---

## Comment 5

> Username: Flast  
> Created_at: 2015-03-06 04:27:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77504929  

Thank you @djowel !

---

## Comment 6

> Username: daminetreg  
> Created_at: 2015-03-06 09:47:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77532675  

Thank you @djowel :)  
  
@Flast we still have the problem of the precompiled headers no ? We cannot let boost get released without them no ?

---

## Comment 7

> Username: Flast  
> Created_at: 2015-03-07 08:22:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77679193  

> @Flast we still have the problem of the precompiled headers no ? We cannot let boost get released without them no ?  
  
Yes, and thank you for #59 and #60 !

---

## Comment 8

> Username: djowel  
> Created_at: 2015-03-07 08:52:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77680116  

Many thanks to you both!

---

## Comment 9

> Username: daminetreg  
> Created_at: 2015-03-07 11:27:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77685348  

I'm happy we could get all we needed in master. Now it will be soon time for user feedback, frightening time. :smile:

---

## Comment 10

> Username: daminetreg  
> Created_at: 2015-03-09 07:32:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77810622  

Regarding the ChangeLog for 1.58.0 I started to put the things together, because Daniel James suggested on devel to make a pull-request with the changelog.  
  
Do you think this is ok : https://github.com/daminetreg/website/commit/d0448a3e0cc5511009e74fbde5682d35e3db51e9 for a changelog for Fusion @djowel and @Flast or did I forgot something ?  
  
``` diff  
+* [phrase library..[@/libs/fusion/ Fusion]:]  
+ * New ADAPT_STRUCT, ADAPT_ADT, ADAPT_ASSOC_ that deduce the members types ([ticket 9516]).  
+ * Add convert implementation for Boost.Tuple and std::tuple.  
+ * Add mpl::clear implementation for Boost.Tuple and std::tuple.  
+ * Use boost::declval instead of std::declval, ([ticket 10190]).  
+ * Fix tuple heading, close ([ticket 5324]).  
+ * Fix document typo, close ([ticket 6090]).  
+ * Remove use of `boost::blank` ([ticket 8622]).  
+ * Add result_of::{copy,move} ([ticket 5886]).  
+ * Better constexpr and noexcept support.  
+ * SFINAE Friendly result_of ([ticket 10676]).  
+ * SFINAE Friendly invoke ([ticket 10443]).  
+ * result_of::size::value and result_of::size::type::value are the same type now ([ticket 7304])  
+ * Fix missing includes ([ticket 8457]).  
+ * result_of::at<Seq, N>::type is now defined when sizeof of sequence is less than N ([ticket 6507], [ticket 7651])  
+ * Many documentation fixes & improvements ([ticket 6090]).  
```

---

## Comment 11

> Username: djowel  
> Created_at: 2015-03-09 09:03:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77819432  

looks good to me.

---

## Comment 12

> Username: Flast  
> Created_at: 2015-03-09 09:14:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77820761  

``` diff  
+ * SFINAE Friendly result_of ([ticket 10676]).  
```  
  
I think it misleads that all result_of are now SFINAE-friendly.  
  
``` diff  
+ * Many documentation fixes & improvements ([ticket 6090]).  
```  
  
Not only 6090, but also `[ticket 5324] [@https://github.com/boostorg/fusion/pull/33 GitHub PR #33] [@https://github.com/boostorg/fusion/pull/53 GitHub PR #53] [@https://github.com/boostorg/fusion/pull/56 GitHub PR #56]`  
  
#12 is nice feature for user.

---

## Comment 13

> Username: daminetreg  
> Created_at: 2015-03-09 10:41:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77831726  

Thanks.  
  
@Flast you think it's worth it to quote all the PRs ?  
  
I added #12, it's indeed good to make people aware from it, I've myself forgotten. :disappointed:   
  
Is it better so ?  
  
https://github.com/daminetreg/website/commit/3b2648850d7187df9d8ad3c89dc6accbb21a0ad8  
  
``` diff  
+ * Automatic hash creation function ([@https://github.com/boostorg/fusion/pull/12 Github PR #12])  
* New ADAPT_STRUCT, ADAPT_ADT, ADAPT_ASSOC_ that deduce the members types ([ticket 9516]).  
* Add convert implementation for Boost.Tuple and std::tuple.  
* Add mpl::clear implementation for Boost.Tuple and std::tuple.  
* Use boost::declval instead of std::declval, ([ticket 10190]).  
- * Fix tuple heading, close ([ticket 5324]).  
- * Fix document typo, close ([ticket 6090]).  
* Remove use of `boost::blank` ([ticket 8622]).  
* Add result_of::{copy,move} ([ticket 5886]).  
* Better constexpr and noexcept support.  
- * SFINAE Friendly result_of ([ticket 10676]).  
- * SFINAE Friendly invoke ([ticket 10443]).  
+ * Improved SFINAE Friendliness of many metafunctions ([ticket 10676], [ticket 10443]).  
* result_of::size::value and result_of::size::type::value are the same type now ([ticket 7304])  
- * Fix missing includes ([ticket 8457]).  
* result_of::at<Seq, N>::type is now defined when sizeof of sequence is less than N ([ticket 6507], [ticket 7651])  
- * Many documentation fixes & improvements ([ticket 6090]).  
+ * Fix missing includes ([ticket 8457]).  
+ * Many documentation fixes & improvements ([ticket 6090] [ticket 5324] [@https://github.com/boostorg/fusion/pull/33 GitHub PR #33] [@https://github.com/boostorg/fusion/pull/53 GitHub PR #53] [@https://github.com/boostorg/fusion/pull/56 GitHub PR #56] )  
  
```

---

## Comment 14

> Username: djowel  
> Created_at: 2015-03-09 10:51:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77832962  

ready to merge?

---

## Comment 15

> Username: Flast  
> Created_at: 2015-03-09 11:01:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77834215  

> it's worth it to quote all the PRs ?  
  
It's better, but not required.  
  
> ready to merge?  
  
I think yes.

---

## Comment 16

> Username: daminetreg  
> Created_at: 2015-03-09 11:30:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/55#issuecomment-77837651  

> ready to merge?  
  
For me it's good. :smile: I made the pull-request to boostorg/website, see https://github.com/boostorg/website/pull/67  
  
If we get the time to link all PRs we can propose the changes again, but I think it's ok that a Changelog is a summary of the changes, because otherwise it could become TLDR; The git commit log gives the detail to those interested.

---
