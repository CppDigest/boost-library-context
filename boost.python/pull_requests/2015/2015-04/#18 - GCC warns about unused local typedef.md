# #18 GCC warns about unused local typedef [Closed]

> Username: alkino  
> Created at: 2015-04-29 11:25:04 UTC  
> Updated at: 2015-05-15 20:13:20 UTC  
> Closed at: 2015-05-15 20:13:20 UTC  
> Url: https://github.com/boostorg/python/pull/18  

I only apply those patchs: https://svn.boost.org/trac/boost/ticket/8888  
  
I don't own those patchs but I need them.

---

## Comment 1

> Username: teeks99  
> Created_at: 2015-05-15 13:33:01 UTC  
> Url: https://github.com/boostorg/python/pull/18#issuecomment-102398240  

I was just going to look at this and make sure it didn't upset anything in MSVC land, but I noticed that the pull request is against master instead of develop. I'm not the owner of this repo, but I thought all of boost was supposed to merge things to develop first.   
  
Maybe this pull request should be closed and a new one opened against develop instead, that would make it much easier for me to check out.  
  
Tom

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2015-05-15 13:43:31 UTC  
> Url: https://github.com/boostorg/python/pull/18#issuecomment-102401314  

Indeed. I'm not sure the pull request can be changed to point to the devel branch instead, or whether this requires a new pull request.  
  
Aside from that, I'm not entirely sure I understand the effect of the patch (sorry, haven't had time to really look into it yet).  
While I'm all for avoiding warnings, I'm somewhat afraid because it seems lots of the constructs here were to add error reporting capabilities, so the compiler would produce error messages that give the user a hint at what is missing (a poor man's substitute for concept checks).  
Other than that, I think the patch(es) look good, and I would like to merge them (to devel).

---

## Comment 3

> Username: blastrock  
> Created_at: 2015-05-15 14:34:52 UTC  
> Url: https://github.com/boostorg/python/pull/18#issuecomment-102415291  

I think a new pull request must be made, but surely there should not be conflicts rebasing this over the devel branch.  
  
As for your concerns about the error reporting, this patch replaces the typedefs with BOOST_MPL_ASSERT_MSG which, I guess, just does the same thing (creating a typedef with the name provided, but with **attribute**((unused)) to avoid the warnings). Only the first occurrence has been replaced by BOOST_STATIC_ASSERT instead, but that can be easily fixed.

---
