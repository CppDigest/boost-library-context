# #85 Feature/no bounds sequence [Merged]

> Username: Flast  
> Created at: 2015-06-23 02:37:48 UTC  
> Updated at: 2015-11-14 08:27:32 UTC  
> Merged at: 2015-06-25 15:09:18 UTC  
> Closed at: 2015-06-25 15:09:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/85  

Relating to https://github.com/boostorg/fusion/commit/c9ae4fc8869faace8eaec797c9c6d90328832f25, introducing `no_bounds_tag` to be able to out-of-bounds access, like  
  
``` cpp  
static_assert(result_of::size<NoboundsUserSequence>::value < 10, "");  
result_of::at_c<NoboundsUserSequence, 10>::type // no error  
```  
  
@jfalcou and @K-ballo, feel free to comment.  
  
Note: This PR is independent from variadics PRs.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-06-23 02:53:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114335227  

Do we really need to introduce a new concept for this? (reading up on c9ae4fc).

---

## Comment 2

> Username: Flast  
> Created_at: 2015-06-23 03:08:19 UTC  
> Updated_at: 2015-06-23 04:09:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114338672  

@jfalcou, Are you still using workaround for `at`? If not, we can simply drop this PR.

---

## Comment 3

> Username: jfalcou  
> Created_at: 2015-06-23 05:08:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114359875  

Yes I am

---

## Comment 4

> Username: Flast  
> Created_at: 2015-06-23 10:00:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114427851  

@djowel , I think that introducing a new concept is safe way to provide old behaviour for who wants it.  
Most users don't use (and don't need) new concept, but specializing under detail namespace is worse than it, isn't it ?

---

## Comment 5

> Username: djowel  
> Created_at: 2015-06-23 23:53:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114676999  

Concepts are sacred :-) We can't just add a concept for this purpose. Imagine adding a new concept for STL like this. Specializing under detail namespace is also not good. There should be another solution. I'd even prefer a macro for disabling index range checking, if that's the main issue.

---

## Comment 6

> Username: Flast  
> Created_at: 2015-06-24 01:49:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114696539  

> Concepts are sacred :-) We can't just add a concept for this purpose. Imagine adding a new concept for STL like this.  
  
Absolutely.  
  
> I'd even prefer a macro for disabling index range checking, if that's the main issue.  
  
OK, It seems like reasonable. I close and open a new PR.

---

## Comment 7

> Username: jfalcou  
> Created_at: 2015-06-24 05:33:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114732549  

a macro make no sense. As a provider of libraries suing fusion, forcing my user to add a macro in their project to have a correct compiling code is rather cumbersome.  
  
I don't see the problem of having a UnboundedRange. It's exactly what the new STL2 based on Eric's range have.

---

## Comment 8

> Username: djowel  
> Created_at: 2015-06-24 05:53:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114735530  

OK, UnboundedSequence sounds good. What are the models of UnboundedSequence then? It will be odd to have such a concept without any models included. Without an example of such a sequence, how are we going to test?

---

## Comment 9

> Username: djowel  
> Created_at: 2015-06-24 05:59:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114738328  

@Flast,  jfalcou makes a compelling case. First off, I think it's the naming that tripped me off. We can start by renaming to Unbounded Sequence and Unbounded Iterator. I'm not sure how we will deal with the model for it and testing, however.

---

## Comment 10

> Username: Flast  
> Created_at: 2015-06-24 06:30:24 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114746155  

> First off, I think it's the naming that tripped me off. We can start by renaming to Unbounded Sequence and Unbounded Iterator.  
  
Unbounded Sequence/Iterator are good name rather than originals.  
  
> I'm not sure how we will deal with the model for it and testing, however.  
  
I'm confusing the difference of model and concept.

---

## Comment 11

> Username: djowel  
> Created_at: 2015-06-24 06:35:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114746957  

> I'm confusing the difference of model and concept.  
  
For example, Random Access Sequence is a concept, fusion::vector is a model of that concept.

---

## Comment 12

> Username: Flast  
> Created_at: 2015-06-24 07:57:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114771662  

> > I'm confusing the difference of model and concept.  
>   
> For example, Random Access Sequence is a concept, fusion::vector is a model of that concept.  
  
Ah, I see.  
  
> What are the models of UnboundedSequence then? It will be odd to have such a concept without any models included.  
  
Unbounded Sequence is not a standalone concept, user should combine it with a traversability concept (i.e. RandomAccess, Forward, ...). My commit message will help: https://github.com/Flast/fusion/commit/1e21a4eb7a4240a9bdb9304ab03ea5761e8a96a3.  
e.g. fusion::vector is a Random Access Sequence, but not a Unbounded Sequence. OTOH, some user sequences are Random Access Sequence and also those are Unbounded Sequence (what @jfalcou wants).

---

## Comment 13

> Username: djowel  
> Created_at: 2015-06-24 08:06:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114775328  

> Unbounded Sequence is not a standalone concept, user should combine it with a traversability concept (i.e. RandomAccess, Forward, ...). My commit message will help: Flast@1e21a4e.  
> e.g. fusion::vector is a Random Access Sequence, but not a Unbounded Sequence. OTOH, some user sequences are Random Access Sequence and also those are Unbounded Sequence (what @jfalcou wants).  
  
Yes, I know. But we do not have any model for it yet, unlike all the other concepts in fusion. So we can't test and improve on it. Is indexing the only feature it needs? How does UnboundedRange in STL2 look like? Perhaps we should look into that for inspiration.

---

## Comment 14

> Username: Flast  
> Created_at: 2015-06-24 16:47:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114938950  

> But we do not have any model for it yet, unlike all the other concepts in fusion. So we can't test and improve on it.  
  
[repetitive_view](http://www.boost.org/doc/libs/1_58_0/libs/fusion/doc/html/fusion/view/repetitive_view.html) seems like a Unbounded Sequence.

---

## Comment 15

> Username: jfalcou  
> Created_at: 2015-06-24 16:53:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-114940233  

actually the concept we need is maybe different.  
  
We need the sequence to have a size BUT any access out of bound return something (either a default value or w/e else). It is ther eto model the injection in a nD space of a mD obejct where m < n.  
So what we need is a way to say here is the size, here's what happens if you at_c out of this size

---

## Comment 16

> Username: Flast  
> Created_at: 2015-06-25 13:48:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/85#issuecomment-115263424  

I renamed the concept to Unbounded Sequence/Iterator as suggested by https://github.com/boostorg/fusion/pull/85#issuecomment-114732549.

---
