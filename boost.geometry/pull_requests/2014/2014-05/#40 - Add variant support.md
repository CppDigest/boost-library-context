# #40 [intersection] Add variant support [Merged]

> Username: sdebionne  
> Created at: 2014-05-23 10:41:41 UTC  
> Updated at: 2014-05-26 08:27:10 UTC  
> Merged at: 2014-05-23 22:32:48 UTC  
> Closed at: 2014-05-23 22:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/40  

This PR adds variant support to the intersection algorithm.  
Note to reviewers : There are a few test cases that do not pass in the original version and this was not fixed. One of them throw a "Boost.Geometry Overlay invalid input exception" and I suspect that the test suite halts at that point (probably avoiding the next test cases).

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-23 12:11:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44001719  

@sdebionne That's wierd, I didn't notice any exceptions. Also here: http://www.boost.org/development/tests/develop/developer/geometry.html the tests are ok.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2014-05-23 12:32:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44003180  

It is indeed reported as passing for my platform / toolset. So there should be something wrong on my side...

---

## Comment 3

> Username: brunolalande  
> Created_at: 2014-05-23 12:38:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44003656  

Or I might be missing something - maybe those particular metafunctions  
happen to pass even with a variant (surprising though). Else it means that  
somehow your variant testing is bypassed...  
  
Bruno

---

## Comment 4

> Username: sdebionne  
> Created_at: 2014-05-23 12:45:46 UTC  
> Updated_at: 2014-05-23 12:46:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44004292  

@brunolalande For the concept::check metafunction, it happens to be specialized for variant and it's a nop. I think the idea was to assert ASAP when instantiating an algorithm with incorrect geometries. So, if it's not a variant, concept::check is effective right in the free function, while, if it's a variant, a second concept::check trap the error after variant resolution.  
Curiously enough other concept checking meta function are not specialized (the one that check dimensions for instance) and must be moved to a later stage.

---

## Comment 5

> Username: brunolalande  
> Created_at: 2014-05-23 12:52:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44004969  

Hmm I don't remember having even done that. I'll look into it. In the  
meantime can you move those checks to the resolve_variant, in the function  
where it calls rsolve_strategy or dispatch? (i.e. at the earliest point  
where we have concrete geometries) This way it won't break when I'll remove  
that bizarre no-op thing.  
  
Thanks  
Bruno

---

## Comment 6

> Username: sdebionne  
> Created_at: 2014-05-23 13:16:13 UTC  
> Updated_at: 2014-05-23 13:22:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44007099  

The concept checks are duplicated (they are already in the function where it calls dispatch). Shall I remove them from the free function ? They are pretty harmless (but usefull with non variant), no?

---

## Comment 7

> Username: brunolalande  
> Created_at: 2014-05-23 14:15:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44013881  

I don't like to duplicate those checks - I agree that they shouldn't impact  
compilation times as the templates have to be instantiated anyway, but I  
don't like redundant things.  
  
Bruno

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2014-05-24 08:53:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/40#issuecomment-44081717  

Hi Samuel,  
  
Thanks for this!!!  
  
One thing, the concept checks should be done as early as possible. Now they are de-duped (OK), but please take the other one...  
  
Regards, Barend

---
