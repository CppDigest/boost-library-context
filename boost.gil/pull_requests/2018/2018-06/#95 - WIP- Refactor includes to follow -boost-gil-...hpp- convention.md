# #95 WIP: Refactor includes to follow <boost/gil/...hpp> convention [Merged]

> Username: mloskot  
> Created at: 2018-06-19 20:32:14 UTC  
> Updated at: 2021-04-21 13:35:04 UTC  
> Merged at: 2018-06-20 12:26:01 UTC  
> Closed at: 2018-06-20 12:26:01 UTC  
> Url: https://github.com/boostorg/gil/pull/95  

WIP: no CI builds enabled yet  
  
### Description: what does this pull request do?  
  
This is non-functional refactoring of headers inclusion to tidy it up and unify with Boost-wide convention.  
  
Group includes in blocks sorted alphabetically within block:  
  
 - `<boost/gil...hpp>`  
 - `<boost/...hpp>`  
 - standard headers  
 - anything else  
  
Rename gil_concept.hpp to concept.hpp  
Rename gil_config.hpp to config.hpp  
Rename utilities.hpp to utility.hpp  
Fix include guards to `BOOST_GIL_*_HPP` convention.  
  
### Questions  
  
1. What policy should we establish regarding forward compatibility of existing Boost.GIL clients?  
Headers are removed, shuffled, renamed - do we want to provide old headers with deprecated warnings?  
  
### Tasklist  
  
- [ ] Resolve question about header-wise compatibility of existing Boost.GIL clients (see Questions)  
- [ ] Clean up `boost/extension` tree  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] Enable CI builds  
- [ ] All CI builds and checks have passed  
  
-----  
  
# Grand Plan  
  
This is first PR of bigger plan which will also include:  
  
1. Clean-up of the header/license comment  
2. Removing of (useless) comment with Doxygen `\file`, `\author`, `\date` etc.  info and moving authors to header/copyright comment.  
3. Clean-up of block comments to use C++ `//` instead of C `/*` style comments  
4. Clean-up of any obviously outdated comments  
5. Removal of `GIL_VERSION` (see #82)  
6. Rename GIL-specific defines to use `BOOST_` prefix (eg. `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED`)  
7. Remove any _"All rights reserved"_  (as per [Do not include "All rights reserved" anywhere](https://www.boost.org/users/license.html) rationale)  
8. Finally, run https://include-what-you-use.org or similar to ensure there are no missing or superfluous `#include`-s.  BTW, the blocked and ordered includes are meant to help in further maintenance of such clean state.

---

## Review 1 [Commented]

> Username: chhenning  
> Created_at: 2018-06-20 12:25:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/95#pullrequestreview-130366837  

Looks good!

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-06-20 12:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398730872  

This change looks OK. To the compatibility question: The extension API(s) aside, I think we should stick to a minimal API that promotes the top-level `boost/gil.hpp` header, which gives us freedom to move anything underneath around without worries for backward compatibility.  
As to the Grand Plan: I understand the temptation, but would suggest that we refrain from any such endeavour until the current set of changes (https://github.com/boostorg/gil/milestone/1) is complete and merged to master.  
Anything else is a distraction, i.e. takes valuable time and focus away from that.  
That being said, slipping a cleanup commit in that follows such a longer-term vision seems fine to me. I just want to make sure we don't take our (collective) eyes off the immediate goal... :-)

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-06-20 12:33:29 UTC  
> Updated_at: 2018-06-20 13:09:36 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398732519  

## I AM REVERTING THIS MERGE  
  
@chhenning **Ouch! This was tagged as WIP (work-in-progress). This was not ready to merge!** I was just asking for initial review.  
  
Next time, I'm going to assign any all my own PRs to myself - that is, I will merge myself following review and agreement to merge.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-06-20 12:46:07 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398735889  

This has been dropped for now until I have time to pick it up and re-submit new PR.  
  
@stefanseefeld The clean-up tasks make me busy only in times when I have no resource to do anything more substantial, late night hacking brainless tasks. I don't mind bikeshedding apparently. However, I'd insist, those are still important to break cultivation of messy/sloppy code structure and to establish some clean no-brainer conventions. We will need to do it sooner or later, for sake of upcoming PRs too.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2018-06-20 12:53:41 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398737999  

Yes, I don't disagree. :-)  
I'm just anxious to complete https://github.com/boostorg/gil/milestone/1, in time for the next release.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-06-20 13:08:54 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398742338  

@stefanseefeld I am being impatient indeed :-) Looks like the merge blunder happened in the right time - it's now easier to postpone this.  
  
Tonight, I'm going to try out the sanitizers.

---

## Comment 7

> Username: chhenning  
> Created_at: 2018-06-20 13:14:01 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398743894  

@mloskot Sorry, I didn't realize this hasn't been tried to compile. I though you would run through the CI before creating pull request.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-06-20 13:16:44 UTC  
> Updated_at: 2018-06-20 13:17:07 UTC  
> Url: https://github.com/boostorg/gil/pull/95#issuecomment-398744678  

@chhenning   
> I though you would run through the CI before creating pull request.  
  
Is that even possible?  
  
I hoped that **WIP** in the PR title as well as **WIP: no CI builds enabled yet** in the description would make the state of the PR clear :)  
  
Let's keep the **WIP** meaning in mind next time :)

---
