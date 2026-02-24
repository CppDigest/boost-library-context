# #7 Use BOOST_ATTRIBUTE_UNUSED from boost/config [Closed]

> Username: blastrock  
> Created at: 2015-04-15 07:37:21 UTC  
> Updated at: 2015-05-20 17:26:16 UTC  
> Closed at: 2015-05-20 17:26:16 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7  

This macro does the proper version check and works against clang 3.6 (which otherwise spits warnings).  
  
I haven't tested this patch. If someone could do it, I would be grateful, otherwise I'll do it myself later.

---

## Comment 1

> Username: Houndie  
> Created_at: 2015-04-29 20:22:07 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97573029  

Solves the same problem as https://github.com/boostorg/concept_check/pull/6 but seems to do it better.  I recommend ignoring my patch and using this one. :-)  
  
You can probably just get rid of the whole #define statement and simply use BOOST_ATTRIBUTE_UNUSED in its place, especially since BOOST_CONCEPT_UNUSED_TYPEDEF is only used in one single location.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-04-29 20:36:01 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97576715  

Im confused about the "does the proper version check" comment by @blastrock.  
  
I see a version check being removed, not added.

---

## Comment 3

> Username: blastrock  
> Created_at: 2015-04-29 21:01:11 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97584614  

Removed the macro as @Houndie suggested, I didn't know if it was the best way to go.  
  
@mclow What I meant is that the version check is already done in boost/config/suffix.hpp, and it is done a better way than it was done here (it actually supports clang 3.6), so this patch just make use of that

---

## Comment 4

> Username: Houndie  
> Created_at: 2015-04-29 21:02:59 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97584969  

I don't think the version check is removed, it's simply reusing the version check work done in boost/config instead of repeating ourselves here.  
  
The "proper" version check has to do with clang.  Clang defines its GCC variables to replicate GCC 4.2, which, with the existing code in concept_check, doesn't add attribute unused (this is because GCC 4.2 was what clang aimed for feature equality with once upon a time).  However, clang does currently do an unused typedef check, so simply checking the GCC version numbers is not enough to satisfy clang.  Presumably the code in boost/config takes that into account.  
  
I tried to do the same sort of fix in my patch, but reusing existing code is the way to go, so I'll probably close mine in favor of this one.

---

## Comment 5

> Username: Houndie  
> Created_at: 2015-04-29 21:04:40 UTC  
> Updated_at: 2015-04-29 21:35:29 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97585345  

Oh and @blastrock I implemented your fix in my code base, and it seems to work for what its worth.  Not really an "official" test but its better than nothing.

---

## Comment 6

> Username: blastrock  
> Created_at: 2015-04-29 21:28:57 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-97591377  

Exactly as @Houndie said :)  
  
And thanks for testing!

---

## Comment 7

> Username: blastrock  
> Created_at: 2015-05-20 12:55:49 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-103874971  

bump  
  
Any chance that this gets merged before the next boost release ?

---

## Comment 8

> Username: ahmedcharles  
> Created_at: 2015-05-20 17:26:12 UTC  
> Url: https://github.com/boostorg/concept_check/pull/7#issuecomment-103967814  

Committed with a small change and on develop, in https://github.com/boostorg/concept_check/commit/bdcd06c4cc1971d763e528b8cb1d0f16fcc5ecf4

---
