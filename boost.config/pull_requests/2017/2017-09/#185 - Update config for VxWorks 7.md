# #185 Update config for VxWorks 7 [Merged]

> Username: kuhlenough  
> Created at: 2017-09-03 00:51:20 UTC  
> Updated at: 2017-10-25 17:30:49 UTC  
> Merged at: 2017-10-25 17:30:05 UTC  
> Closed at: 2017-10-25 17:30:05 UTC  
> Url: https://github.com/boostorg/config/pull/185  

This request updates vxworks.hpp and couple other files to the current version of VxWorks.    
We validated with the complete set of libraries, and found a few configuration items that apply to all versions.

---

## Comment 1

> Username: kuhlenough  
> Created_at: 2017-09-04 04:20:58 UTC  
> Url: https://github.com/boostorg/config/pull/185#issuecomment-326862868  

Travis' clang build error looks unrelated to like pull request?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-09-04 07:34:55 UTC  
> Url: https://github.com/boostorg/config/pull/185#issuecomment-326887641  

On 04/09/2017 05:20, Brian Kuhl wrote:  
>  
> Travis' clang build error looks unrelated to like pull request?  
>  
  
Nod.  Pushed a tentative fix, not sure why that's suddenly started   
showing up :(  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2017-09-05 19:43:12 UTC  
> Url: https://github.com/boostorg/config/pull/185#issuecomment-327282138  

reopen just to trigger validation builds

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2017-09-06 18:10:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/185#pullrequestreview-61001977  

📁 include/boost/config/platform/vxworks.hpp

```diff
  33 |+ //  When building the log library add the following to the b2 invocation
  34 |+ //     define=BOOST_LOG_WITHOUT_IPC
  35 |+ //  and
```

> Username: jzmaddock  
> Created_at: 2017-09-06 18:10:41 UTC  
> Url: https://github.com/boostorg/config/pull/185#discussion_r137346948  

If you haven't done so already, it would be worthwhile submitting PR's to these individual libraries for these library-specific macros.

> Username: kuhlenough  
> Created_at: 2017-09-06 19:14:19 UTC  
> Url: https://github.com/boostorg/config/pull/185#discussion_r137364060  

Sure, I'm a little more confident modifying things these days, so I'll try crafting a more elegant solution than overloading the b2 invocation.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2017-10-25 17:25:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/185#pullrequestreview-71945032  

📁 include/boost/config/compiler/diab.hpp

```diff
  17 |+ 
  18 |+ #define BOOST_MPL_CFG_NO_HAS_XXX_TEMPLATE
  19 |+ #define BOOST_LOG_NO_MEMBER_TEMPLATE_FRIENDS
```

> Username: jzmaddock  
> Created_at: 2017-10-25 17:25:56 UTC  
> Url: https://github.com/boostorg/config/pull/185#discussion_r146928344  

This should really be changed in Boost.Log.

> Username: jzmaddock  
> Created_at: 2017-10-25 17:29:45 UTC  
> Url: https://github.com/boostorg/config/pull/185#discussion_r146929439  

Never mind, I don't see why this should hold things up, will merge.

> Username: kuhlenough  
> Created_at: 2017-10-25 17:30:49 UTC  
> Url: https://github.com/boostorg/config/pull/185#discussion_r146929723  

Noted, I'll incorporate it into my pull for Boost.Log, (that admittedly been ongoing for a few weeks,) I'll get back to it soon. - and update this one as well.


---
