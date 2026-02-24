# #31 Add support, macros and versionning for SIMD extensions [Merged]

> Username: ccharly  
> Created at: 2015-07-15 14:40:25 UTC  
> Updated at: 2015-08-07 01:37:23 UTC  
> Merged at: 2015-08-07 01:37:23 UTC  
> Closed at: 2015-08-07 01:37:23 UTC  
> Url: https://github.com/boostorg/predef/pull/31  

This PR provides a new set of predefs about potential usage of SIMD extensions through compiler settings.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2015-07-15 16:57:27 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121679235  

I will not have time to merge this properly until I get back home in August (i.e. after the Boost release). But I'll try and comment as best I can until then.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2015-07-15 17:00:16 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34701665  

BOOST_HW_SIMD_x86 should be BOOST_HW_SIMD_X86

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2015-07-15 17:03:31 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34702026  

Should that be 'SSE3'? I.e. two S's instead of three?

---

## Comment 4

> Username: ccharly  
> Created_at: 2015-07-15 17:07:43 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34702449  

yes you're right for this one!

---

## Comment 5

> Username: ccharly  
> Created_at: 2015-07-15 17:10:07 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34702744  

nope, in that case we really want to check if we are between SSE2 and **S**SSE3 (the extra **S** stands for **S**upplemental), but yes there is also SSE3... The order is:  
  
> SSE -> SSE2 -> SSE3 -> SSSE3 -> ...

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2015-07-15 17:19:19 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34703772  

This needs to follow the same pattern as the other category includes, for example [architecture.h](https://github.com/boostorg/predef/blob/develop/include/boost/predef/architecture.h). As otherwise the testing detection doesn't work. Which you can see in the CI test output. The general rule for grouping headers like this is that for testing to work all headers must be able to be included multiple times, down to the lowest level. Hopefully that explains the strange ifdef constructs.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2015-07-15 17:26:13 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34704619  

Again, because of testing, these need to go outside the file level ifdef. For an example see [x86.h]](https://github.com/boostorg/predef/blob/develop/include/boost/predef/architecture/x86.h).

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2015-07-15 17:28:10 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34704854  

Needs to go outside file level ifdef, re testing.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2015-07-15 17:35:30 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34705704  

Needs to go outside file level ifdef, re testing.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2015-07-15 17:37:51 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34705969  

Needs to go outside file level ifdef, re testing.

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2015-07-15 17:48:55 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34707334  

I think I would rather see some form of descriptive name rather than "(not found)", and also a consistent name rather than being defined to the subcategory detected name. The NAME was always meant to be usable isolated. And having something generic like "(not found)" goes against that.

---

## Comment 12

> Username: ccharly  
> Created_at: 2015-07-15 19:21:24 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34718137  

Alright, I see. What would you suggest? Something like: "PowerPC SIMD category"? Or maybe "PowerPC most recent extension"?

---

## Comment 13

> Username: grafikrobot  
> Created_at: 2015-07-15 19:31:09 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34719108  

I'd say just "PowerPC SIMD". And possibly "PowerPC SIMD QPX" for BOOST_HW_SIMD_PPC_QPX_NAME. And so forth.

---

## Comment 14

> Username: ccharly  
> Created_at: 2015-07-15 19:36:20 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r34719636  

Sounds good to me! I'm on it.

---

## Comment 15

> Username: ccharly  
> Created_at: 2015-07-15 20:22:22 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121735381  

I've updated everything as you suggested. Tell me if you spot everything else.

---

## Comment 16

> Username: grafikrobot  
> Created_at: 2015-07-15 20:52:34 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121743773  

There's something wrong with the checks. In the CI testing I see all the SIMD lower level defs showing up in the detected section as defined: https://ci.appveyor.com/project/boostorg/predef/build/1.0.18/job/vsv48lahg517s2ei#L1015 and https://travis-ci.org/boostorg/predef/jobs/71143737#L607.  
  
Yet the SIMD categories don't show up anywhere. The latter I now see is because you don't add test lines for the categories. So.. Since the categories have definitions, please add test lines for them also.  
  
And obviously, the lower level defs need to be zero if not detected.

---

## Comment 17

> Username: ccharly  
> Created_at: 2015-07-15 21:04:50 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121746965  

Ok, I'll add these tests. About detection of lower level defs, are you talking about SSE2, SSE3, NEON, etc.. all those kinds of predefs? If yes, then @jfalcou and I decided to always give a valid number version for all these predefs. The `_AVAILABLE` is defined if we detect them. The `BOOST_HW_SIMD_"FAMILY"_"EXT"` have a static version.  
  
We thought that it was a good use for version system. Because while dealing with SIMD extension you want to write those kind of expressions:  
  
``` cpp  
#if BOOST_HW_SIMD_X86 <= BOOST_HW_SIMD_X86_SSE3  
...  
#endif  
```  
  
This is not a valid example if `BOOST_HW_SIMD_X86` and `BOOST_HW_SIMD_X86_SSE3` are defined as `BOOST_VERSIO_NUMBER_NOT_AVAILABLE` (if none of them have been detected).

---

## Comment 18

> Username: grafikrobot  
> Created_at: 2015-07-16 02:31:08 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121805845  

OK.. I'm looking more carefully now.. And I'm afraid your method is just not going to cut it. I understand that you want to use the comparison between them, but it currently has a serious drawback:  
  
It violates one of the big rules of Predef.. that a definition is always zero when not detected. And non-zero if detected. Hence it violates user expectations and will eventually lead to hard to detect bugs. As users will assume these defs work just like all the others.  
  
So, sorry, but I need you to figure out some other way to accomplish what you want. Or to abandon the idea of using the relative comparisons. But all the predefs need to be zero on non-detection. I must have consistency in the design and operation.

---

## Comment 19

> Username: ccharly  
> Created_at: 2015-07-16 06:08:47 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121843569  

I understand. I've started writing those predefs like the others (with a default value of `BOOST_NUMBER_VERSION_NOT_AVAILABLE`) but then, I thought that it was not that easy to use.. (e.g. for `<`, `>`, `<=`, `>=` comparisons). So I've changed my mind and started to write them "differently".  
  
One easy fix I'm thinking about would be to totally drop those kind of comparisons to use only `==` comparison. But obviously this is less intuitive and you also have to enumerate every `BOOST_HW_SIMD_"FAMILY"_"EXT"` that you want to test, which may result on a really really big condition.  
  
On the other hand, we could still use these operators, but we have to add extra checks to know if extensions have been detected:  
  
``` cpp  
// In that case you can remove the check for `BOOST_HW_SIMD_X86_SSE2` as it is implicitly included  
// by `BOOST_HW_SIMD_X86_SSSE3`. But some other extensions are AMD specific and they dont  
// always are implicitly included by more recent extensions (e.g. `BOOST_HW_SIMD_X86_XOP`)  
#if BOOST_HW_SIMD_X86 && BOOST_HW_SIMD_X86_SSE2 && BOOST_HW_SIMD_X86_SSSE3 &&\  
    BOOST_HW_SIMD_X86 >= SSE2 && BOOST_HW_SIMD_X86 <= SSSE3  
...  
#endif  
```  
  
Which (imho) is not really intuitive too...  
  
If you have any other ideas on how to deal with these comparisons then let me know. Meanwhile, 'm going to think about something else that stick more to `predef`'s philosophy.

---

## Comment 20

> Username: ccharly  
> Created_at: 2015-07-16 06:28:26 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121845863  

Maybe some kind of helpers could get rid of the lack of redundant checks? For example:  
  
``` cpp  
#include <iostream>  
  
#define _10 10  
#define _20 20  
#define _30 30  
  
#define _10_AVAILABLE  
#define _20_AVAILABLE  
#define _30_AVAILABLE  
  
#define BOOST_PREDEF_COMP(LHS, OP, RHS)\  
    defined(LHS##_AVAILABLE) && defined(RHS##_AVAILABLE) && LHS OP RHS  
  
#define BOOST_PREDEF_COMP_RANGE(LHS, OP1, RHS1, OP2, RHS2)\  
    defined(LHS##_AVAILABLE) && defined(RHS1##_AVAILABLE) && defined(RHS2##_AVAILABLE) &&\  
    LHS OP1 RHS1 && LHS OP2 RHS2  
  
int main() {  
    #if BOOST_PREDEF_COMP(_10, ==, _10)  
    std::cout << "10 == 10: true" << std::endl;  
    #endif  
  
    #if BOOST_PREDEF_COMP(_10, <, _20)  
    std::cout << "10 < 20: true" << std::endl;  
    #endif  
  
    #if BOOST_PREDEF_COMP_RANGE(_20, >=, _10, <=, _30)  
    std::cout << "20 >= 10 && 20 <= 30: true" << std::endl;  
    #endif  
  
    return 0;  
}  
```  
  
Something like that? But.. Should those helpers go into `boost.predef`?  
  
In our case, we could still defined those helpers for us and use them. This way, we could rewrite all SIMD's predefs with zero and non-zero version number if detected. I've to discuss this potential solution with @jfalcou though...

---

## Comment 21

> Username: ccharly  
> Created_at: 2015-07-16 06:29:29 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121845964  

btw, @jfalcou any other ideas?

---

## Comment 22

> Username: jfalcou  
> Created_at: 2015-07-16 09:34:08 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121899761  

I think that we should go on with Predef idioms and have those 0-valued version if not detected. As for helpers, I think we can provide them in Boost.SIMD. No need to horseshoe everything there.  
  
A quick look at the current Boost.SIMD usage of these macro, we only need the range for one very specific case, so let's specialize it on our side.

---

## Comment 23

> Username: ccharly  
> Created_at: 2015-07-16 09:43:38 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121904585  

Ok, I'm on it.

---

## Comment 24

> Username: ccharly  
> Created_at: 2015-07-16 13:41:20 UTC  
> Updated_at: 2015-07-16 13:46:31 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-121958845  

Alright, before rewriting everything, I would like to have your opinions (@grafikrobot, @jfalcou) about something that could be new in `boost.predef`. This is something that has a different philosophy but this is also something that could be generalized in the future.  
  
We could stay with the current detection of extensions and only use it to give a value to `BOOST_HW_SIMD_"FAMILY"`. For example, for `BOOST_HW_SIMD_X86`, we test every extensions from the newest to the oldest to get the version of the most recent detected extension. This is actually pretty similar to [this](https://github.com/boostorg/predef/blob/develop/include/boost/predef/os/bsd.h#L68-L82).  
  
We try to detect, if we detect, then we define with a specific value.  
  
In this example, the given number versions are pretty straightforward and easy to use, so you can easily write this:  
  
``` cpp  
#if BOOST_OS_BSD >= BOOST_VERSION_NUMBER(4, 2, 0)  
...  
#endif  
```  
  
But we could also create mapping between version names and version number, for example:  
  
``` cpp  
#define BOOST_OS_BSD4_2_VERSION BOOST_VERSION_NUMBER(4, 2, 0)  
```  
  
And use it like:  
  
``` cpp  
#if BOOST_OS_BSD >= BOOST_OS_BSD4_2_VERSION  
...  
#endif  
```  
  
This is a simple example in that case, but I think that this pattern is actually pretty common. Also, this is more error-proof. If you miswrite a version, then the preprocessor will tell you that you are using a invalid token.  
  
Introducing a new notion of `_VERSION` could help the user to easily replace strange number version by a better name.  
  
Another better example would be Windows version. This is a not real example since there is no way to get the OS version of Windows at preprocessing though... But this is a good example to illustrate the usefulness of `_VERSION` defines. People usually knows Windows OS versions by their name, for example `Windows VISTA` is `6.0.6002`. This is a lot easier to use a name that gives its version than using the number version directly:  
  
``` cpp  
#if BOOST_OS_WINDOWS == BOOST_VERSION_NUMBER(6, 0, 6002)  
...  
#endif  
  
// versus.  
  
#if BOOST_OS_WINDOWS == BOOST_OS_WINDOWS_VISTA_VERSION  
...  
#endif  
```  
  
To sum up, we could remove every `BOOST_HW_SIMD_"FAMILY"_*` predefs and use only `_VERSION` for extensions. The only remaining predefs would be the `BOOST_HW_SIMD_"FAMILY"` (where `"FAMILY" are X86, ARM, PPC). So we could them like this:  
  
``` cpp  
#if BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE2_VERSION &&\  
    BOOST_HW_SIMD_X86 <= BOOST_HW_SIMD_X86_SSSE3_VERSION  
...  
#endif  
```  
  
What do you think?

---

## Comment 25

> Username: ccharly  
> Created_at: 2015-07-17 07:42:25 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122205979  

@jfalcou, @grafikrobot, just a little ping! :)

---

## Comment 26

> Username: jfalcou  
> Created_at: 2015-07-17 07:48:35 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122207086  

For me it makes sense. Now to see what @grafikrobot  thinks about it

---

## Comment 27

> Username: grafikrobot  
> Created_at: 2015-07-17 11:41:33 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122252931  

I thought about this same idea yesterday, and erased that part of my reply :-) So I'm not totally against it. It has pros and cons..  
  
(a) Pro: As you say it's easier to use for definitions that have commonly known names. Which I think is a big pro for users.  
  
(b) Pro: Makes the user code more readable, and less error prone.  
  
(c) Con: It's more maintenance for me. As each time a new version of something comes out I would have to add _VERSION defs for those new versions. Which given that I don't keep track of everything that gets updated in the computing world.. It would mean that those version numbers would never be up to date. And users would not have the latest defs they would expect.  
  
(d) Con: It's not clear to me how to deal with patch releases for those things. For example, what would the BOOST_OS_WINDOWS_VISTA_VERSION def be set to for a later patch release of Vista as it encompasses a range of version numbers?

---

## Comment 28

> Username: jfalcou  
> Created_at: 2015-07-17 11:59:52 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122255966  

@grafikrobot  what about using this system for element of the configuration that dont change too much. SIMD extensions will probably handled by us anyway and dont spawn too fast. For other elements, we can see to provide version of old stable ones and go forward.

---

## Comment 29

> Username: ccharly  
> Created_at: 2015-07-17 12:18:13 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122258758  

@grafikrobot yes, maybe we should use them for only a restricted set of versions? I don't have any other ideas to deal with (d)...  
Anyway, that could be an elegant solution to deal with SIMD extensions at least and we could probably use it elsewhere. Only the patch releases problem remains. At least giving "big" family of versions could definitely help most of the users.

---

## Comment 30

> Username: grafikrobot  
> Created_at: 2015-07-17 13:15:11 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122272428  

I'm fine with doing this for the SIMD defs only for now. And then investigate adding them in other places where I can balance the pros/cons. I suggest you document the VERSION defs in the SIMD def reference (as opposed to the general documentation).

---

## Comment 31

> Username: ccharly  
> Created_at: 2015-07-17 13:28:45 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122274829  

Sounds good to me! Thanks. :)

---

## Comment 32

> Username: ccharly  
> Created_at: 2015-07-20 13:04:07 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122877079  

I'm modifying the documentation for now. Is there any easy way to build the documentation on my own? I would like to make sure that everything is in place.

---

## Comment 33

> Username: ccharly  
> Created_at: 2015-07-20 13:36:00 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-122886273  

Ok, I've found how to generate it! :)

---

## Comment 34

> Username: ccharly  
> Created_at: 2015-07-21 08:27:16 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-123211690  

I've finished refactoring what we discussed earlier. The PR is quite big for now, and the first commits are  no longer related to what is the PR now. Would you prefer to rewrite the history of this PR to get only commits that make sense? Or we can leave it as is now?

---

## Comment 35

> Username: grafikrobot  
> Created_at: 2015-07-21 13:24:35 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35100571  

This is superfluous now?

---

## Comment 36

> Username: ccharly  
> Created_at: 2015-07-21 13:30:33 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35101133  

indeed

---

## Comment 37

> Username: grafikrobot  
> Created_at: 2015-07-21 13:32:57 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35101356  

This should just be a section for BOOST_HW. Like the other sections above it.

---

## Comment 38

> Username: grafikrobot  
> Created_at: 2015-07-21 16:18:49 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35121886  

Wrong version number here.

---

## Comment 39

> Username: grafikrobot  
> Created_at: 2015-07-21 16:26:43 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35122780  

That number is outside of the allowed range. Max currently allowed is 99.99.99999. And is the "max" what you really want here? PS. And if you do want max there's a def for that `BOOST_VERSION_NUMBER_MAX` you can use instead of the hard-coded value.

---

## Comment 40

> Username: grafikrobot  
> Created_at: 2015-07-21 16:31:23 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35123321  

Wrong version number here.

---

## Comment 41

> Username: ccharly  
> Created_at: 2015-07-21 18:03:35 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35133691  

We decided to put it at the max version number for now. Thanks for the tip! Didn't notice that `BOOST_VERSION_NUMBER_MAX`.

---

## Comment 42

> Username: jfalcou  
> Created_at: 2015-07-21 18:15:05 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35135057  

this probably be only 9.0 for now

---

## Comment 43

> Username: ccharly  
> Created_at: 2015-07-21 18:29:01 UTC  
> Updated_at: 2015-08-04 10:01:17 UTC  
> Url: https://github.com/boostorg/predef/pull/31#discussion_r35136763  

ok, i'm changing that then.

---

## Comment 44

> Username: grafikrobot  
> Created_at: 2015-08-06 19:33:34 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-128481826  

I'd like to merge this soon.. So I'm wondering if you are done with tweaks on it?

---

## Comment 45

> Username: ccharly  
> Created_at: 2015-08-06 20:11:37 UTC  
> Url: https://github.com/boostorg/predef/pull/31#issuecomment-128492209  

I'm done with that. Thanks! :)

---
