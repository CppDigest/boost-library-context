# #28 Feature/x3 repeat [Merged]

> Username: teajay-fr  
> Created at: 2014-05-07 13:01:57 UTC  
> Updated at: 2014-06-26 22:19:14 UTC  
> Merged at: 2014-05-30 05:26:32 UTC  
> Closed at: 2014-05-30 05:26:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/28  

This implements the repeat directive just as in spirit v2.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2014-05-07 13:26:57 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12375822  

Instead of a type-erased function, should the repeat_directive take the callbacks as template arguments? That would allow for some inlining/optimizations by the compiler. This may require separate static functions that create and return the lambdas, so the type can be determined with declval.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2014-05-07 20:28:32 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12400471  

I think the tests should look at the iterator positions. The v2 repeat parser only modifies `first` when true is returned. This (line 43-44) is updating the iterator every time the subject passes. I think the iterator needs to rollback when the minimum value has not been reached.

---

## Comment 3

> Username: vtnerd  
> Created_at: 2014-05-07 20:34:48 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12400843  

This save variable is needed by the first loop, but not this second loop. Since the repeat parser always succeeds after the minimum value has been reached, I think you can use the `first` variable directly. The subject is required to rollback the iterator position when parsing fails. I guess a bit of pessimism (defensive coding?) wouldn't hurt though.

---

## Comment 4

> Username: vtnerd  
> Created_at: 2014-05-07 20:36:11 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12400946  

I noticed K-Ballo started using private members in some areas, I think we should do the same. These can both be private and const. Same thing for the temporary object below.

---

## Comment 5

> Username: teajay-fr  
> Created_at: 2014-05-07 20:37:11 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12400994  

Good point. I'll extend the tests and correct the code.

---

## Comment 6

> Username: teajay-fr  
> Created_at: 2014-05-07 20:39:47 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12401157  

I think you are right. I think I was thinking of the exact parser at this point. But as you said, the parser just stops, when the min count has been reached in that case.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2014-05-08 01:42:54 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12412639  

The purpose of `RepeatIterator` is unclear to me. What is it, and why is it needed?

---

## Comment 8

> Username: K-ballo  
> Created_at: 2014-05-08 01:50:55 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12412794  

If I did so it was just a force of habit, as it would be inconsistent with the rest of X3 that uses structs with public members. That said, I'd be glad if the decision is to consistently use private members, possibly with names starting or ending in an underscore. All those members are implementation details so private or not the user should not be touching them, but it would be nice to have some guarantee that no other implementation detail is relying on them.

---

## Comment 9

> Username: djowel  
> Created_at: 2014-05-08 02:19:24 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12413244  

classes and private members just get in the way with little benefit in a const world. -- in my opinion.

---

## Comment 10

> Username: K-ballo  
> Created_at: 2014-05-08 02:34:38 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12413507  

The benefit I was talking about is that of having `friend` declarations document the coupling between otherwise seemingly independent classes, so that one can be aware that changing an implementation detail may ripple.

---

## Comment 11

> Username: djowel  
> Created_at: 2014-05-08 02:37:34 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12413568  

Do we have coupling?

---

## Comment 12

> Username: K-ballo  
> Created_at: 2014-05-08 02:48:05 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12413723  

Well, that's the whole point... Who would know without checking every other single line of code?

---

## Comment 13

> Username: djowel  
> Created_at: 2014-05-08 03:08:16 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12414045  

I avoid coupling as much as I can. If there is coupling, by design (e.g. the TST and nodes), I use a class or struct with private members. I also do that when the class/struct is presented as-is to the world (again e.g. TST things). FWIW, I dislike friends (at least the c++ types :-P ... i hate it that friends can access private parts!).  
  
That being said, I am not against classes and private members as long as there's consistency. But we have to ask ourselves one very important question: is it really worth it? After more than a decade with Fusion, Spirit and Phoenix, I've never encountered a real problem stemming from this privacy issue.

---

## Comment 14

> Username: K-ballo  
> Created_at: 2014-05-08 04:09:35 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12414746  

Whether it's worth it probably depends more on the rest on the world than on us. I can certainly keep a bunch of information in the back of my mind, so in the end I don't really care. However, I prefer to convey that information explicitly in the code when language features make it possible, implicitly through conventions otherwise. The back of my mind is a last resort option.

---

## Comment 15

> Username: teajay-fr  
> Created_at: 2014-05-08 05:19:18 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12415694  

The RepeatIterator is the type of the counter used internally. I'm not sure it's really needed, I could go with std::size_t but I'm not sure it's a good idea.

---

## Comment 16

> Username: teajay-fr  
> Created_at: 2014-05-08 05:23:42 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12415734  

I'm going back to using the same struct functors as in V2. Using lambdas here brings more problems than anything.  I couldn't figure out how to get the lambda type which depends on the RepeatIterator type. It doesn't really matter, it's just a bit more text.

---

## Comment 17

> Username: K-ballo  
> Created_at: 2014-05-08 13:32:13 UTC  
> Updated_at: 2014-05-08 21:25:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#discussion_r12428195  

The name `Iterator` fooled me. I understand that all you actually do with it is increment it, and maybe compare for equality?

---

## Comment 18

> Username: vtnerd  
> Created_at: 2014-05-29 01:55:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#issuecomment-44486362  

A couple of things:  
1. Joel doesn't deal with crap programmers who break encapsulation, so remove the private.  
2. A new test function, `test_failure` can be used to ensure iterator rollback.  
3. The interface to parse has changed, so this will need to be updated.

---

## Comment 19

> Username: djowel  
> Created_at: 2014-05-29 03:45:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#issuecomment-44491592  

" Joel doesn't deal with crap programmers who break encapsulation, so remove the private."  
  
LOL. Not entirely true. I still use private when it makes sense. In this case, I trust the programmer to do the right thing: with expression templates, you have this object that will be part of a typically very deep object composition (i.e. your grammar or rule). You'll have to get really crafty to get the nth node deep in the hierarchy just to subvert the intent of the API. If one wants to really do that, so be it, but caveat emptor. A crafty programmer can subvert the code somehow anyway, regardless if it's private.

---

## Comment 20

> Username: djowel  
> Created_at: 2014-05-30 05:26:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#issuecomment-44616237  

I'm going to merge this and work on the necessary adjustments to have things going forward. Let's see how it goes.

---

## Comment 21

> Username: teajay-fr  
> Created_at: 2014-05-30 21:38:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#issuecomment-44702518  

Thanks !

---

## Comment 22

> Username: djowel  
> Created_at: 2014-05-31 05:28:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/28#issuecomment-44718530  

it's all working now. i'll merge the other changes after the weekend.

---
