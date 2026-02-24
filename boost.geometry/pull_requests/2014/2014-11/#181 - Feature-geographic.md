# #181 Feature/geographic [Merged]

> Username: awulkiew  
> Created at: 2014-11-17 01:41:21 UTC  
> Updated at: 2014-11-19 16:49:28 UTC  
> Merged at: 2014-11-19 16:49:28 UTC  
> Closed at: 2014-11-19 16:49:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/181  

The purpose of this PR is the "official" release of strategies used in geographic CS currently in extensions.  
  
The first commit adds functions and metafunction for accessing radius values and type of various geometries: sphere, spheroid, circle, etc. This is a tweaked version of a part of Nsphere concept (in extensions). It was extended with pointers support and the use of util::bare_type<>. It also adds implementations of 2 models cs::model::sphere<> and cs::model::spheroid<>.  
  
The second commit uses replaces the use of detail::ellipsoid<> with the Spheroid concept.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-11-17 17:11:31 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20448930  

Please, please add {} here and in all similar places, also in the future, to fulfil the guidelines, make code more error-proof and easily readable.  
Alternatively (in this case) you can easily use the ?: operator to have all in one line.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-11-17 17:14:14 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449096  

Shouldn't we make flattening part of the Spheroid Concept?  
It is used everywhere (in geographic plus projections), we don't want to write out this calculation again and again...

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-11-17 17:15:03 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449140  

e.g. here it is (I assume) the same

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-11-17 17:16:27 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449220  

Why a C-cast here?  
I think the original was OK and also running using TTMath

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-11-17 17:17:06 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449254  

You probably should not cast this. At least not like this.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-11-17 17:25:11 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449759  

It was added here because in MSVC this gave warnings. Btw, this isn't a C-cast (it'd be `(CT)1e-12` but C++ initialization using ctor.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-11-17 17:26:23 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20449837  

It'd be casted anyway but give warnings in some cases, but sure I may use static_cast. Or do you think that it shouldn't be explicitly casted at all?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-11-17 19:07:08 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20456512  

I sent an email about it. The problem is that the flattening would have to be stored using some FP type, this would complicate the Spheroid Concept. But of course you're right. For convenience I added `bg::detail::flattening()` algorithm. Are you ok with this solution?

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-11-18 13:22:59 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20504189  

I am not sure whether the discussion about this has been finalized.  
Personally, I do not like the idea of having the reference system models inside the `cs` namespace.  
I would go for `rs` (as proposed by Adam), or `srs` as proposed by Barend. Most probably, `rs` is more generic.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-11-18 13:23:52 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20504227  

What happens if the radius type is given as an int?  
Don't we want to do some kind of type promotion?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2014-11-18 14:34:37 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20508199  

IMO no because it means that the user choose this type on purpose. This is just a default ctor, the user may pass different values using the other ctor.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-11-19 15:38:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#issuecomment-63658718  

I moved the reference models to the srs namespace as suggested. IMO the PR is ready for merging. I'd like to move vincenty and andoyer strategies in a different one.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2014-11-19 16:21:29 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20586432  

OK - indeed  a constructor.  
I think this is problematic for ttmath but it's no problem to merge it yet, it can be solved afterwards.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-11-19 16:23:29 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20586606  

In the very first versions of Boost.Geometry we used one-character-template arguments (G, D). This is probably still a relict from those days. Later we used full words (Geometry, Dimension).  
It's no problem to merge it yet, but for your information.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2014-11-19 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#issuecomment-63667250  

I'm OK with merging.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-11-19 16:33:51 UTC  
> Updated_at: 2014-11-19 16:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/181#discussion_r20587548  

Yes this was just copied from exensions. I may "fix" it and merge afterwards.

---
