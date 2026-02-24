# #84 Variadic fusion::list. [Merged]

> Username: Flast  
> Created at: 2015-06-22 14:22:38 UTC  
> Updated at: 2015-07-01 01:17:35 UTC  
> Merged at: 2015-07-01 00:35:47 UTC  
> Closed at: 2015-07-01 00:35:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/84  

As mentioned in #57, this implementation doesn't support move.  
I'll fix it until 1.59 release.

---

## Comment 1

> Username: Flast  
> Created_at: 2015-06-22 15:07:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-114144764  

Tested:  
- GCC 5.1.1 gnu++98 / gnu++11 / gnu++14  
- Clang 3.6.1 gnu++98 / gnu++11 / gnu++14  
- MSVC 9.0 / 10.0 / 11.0 / 12.0

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-06-22 17:01:12 UTC  
> Updated_at: 2015-06-24 06:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r32957222  

I think it should be possible to drop this specialization by simply giving `list_to_cons<>` a `call` static member function. Have you considered it?

---

## Comment 3

> Username: djowel  
> Created_at: 2015-06-22 23:23:30 UTC  
> Updated_at: 2015-06-24 06:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r32995533  

Wouldn't this be a good time to support move as well?

---

## Comment 4

> Username: Flast  
> Created_at: 2015-06-22 23:32:23 UTC  
> Updated_at: 2015-06-24 06:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r32996163  

Indeed. I'll drop it.

---

## Comment 5

> Username: Flast  
> Created_at: 2015-06-23 02:46:29 UTC  
> Updated_at: 2015-06-24 06:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r33005283  

You mean, preprocessed one would also support move now?

---

## Comment 6

> Username: djowel  
> Created_at: 2015-06-23 04:47:05 UTC  
> Updated_at: 2015-06-24 06:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r33009104  

I mean the C++11 version (C++11 interface)

---

## Comment 7

> Username: Flast  
> Created_at: 2015-06-24 16:33:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#discussion_r33167281  

@djowel , I added move ctor/assgin for C++11 interface https://github.com/Flast/fusion/commit/d5ce74dffd0d461a931dc90cb36b5919ba80f99b , but not for C++03 and cons because modifying cons is a deal (the ctor of cons requires overall reimplementing).  
Is this OK in this PR?

---

## Comment 8

> Username: Flast  
> Created_at: 2015-06-29 11:51:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-116626515  

ping?

---

## Comment 9

> Username: djowel  
> Created_at: 2015-06-30 23:58:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117375094  

Looks good to me. @K-ballo ? It's not perfect, but again, we can incrementally improve this.

---

## Comment 10

> Username: K-ballo  
> Created_at: 2015-07-01 00:05:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117375766  

There's not much for me to do here, a list like this is inherently recursive and thus compile time inefficient. I'm not going to pretend to understand what the value is in having a compile time fixed size heterogeneous list.

---

## Comment 11

> Username: djowel  
> Created_at: 2015-07-01 00:23:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117377680  

Those are very good points. I've been asking myself that same question too, but this one is historical. Arguably, the value is in push_front, but I'm not sure that is relevant anymore, esp. with Fusion's lazy views. I was thinking about deprecating them, but I don't see the point in doing that either. I'd love to hear your and Flast's thoughts.

---

## Comment 12

> Username: K-ballo  
> Created_at: 2015-07-01 00:32:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117378566  

I don't see the point in it, so I'd be fine with taking this PR as-is and deprecating it. I would definitively not include it in a v3 unless someone can think of a reason.

---

## Comment 13

> Username: djowel  
> Created_at: 2015-07-01 00:34:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117378771  

Agreed!

---

## Comment 14

> Username: Flast  
> Created_at: 2015-07-01 01:10:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117383349  

> I would definitively not include it in a v3 unless someone can think of a reason.  
  
Agreed too!  
  
.o0(If we go to v3, I wonder we implement sequences other than tuple...

---

## Comment 15

> Username: K-ballo  
> Created_at: 2015-07-01 01:15:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117384227  

> .o0(If we go to v3, I wonder we implement sequences other than tuple...  
  
Map and set would be nice, associative only, non-indexable.

---

## Comment 16

> Username: Flast  
> Created_at: 2015-07-01 01:17:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/84#issuecomment-117384728  

> Map and set would be nice, associative only, non-indexable.  
  
Ah, yes. I had forgotten those.

---
