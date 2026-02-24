# #446 - Weighted B-Matching [Open]

> Username: np96  
> Created at: 2026-01-08 13:42:30 UTC  
> Updated at: 2026-01-15 01:09:11 UTC  
> Url: https://github.com/boostorg/graph/issues/446  

Any plans on implementation of [Blossom V](https://pub.ista.ac.at/~vnk/papers/blossom5.pdf) or another b-matching algorithm?  
I need to use it in my project. Although I can download Vladimir's code, I'd be happy to see it in Boost.  
Looks like it is not difficult to rewrite using Boost.Graph codebase. So, the question is: why is it not there yet? I may try to submit a PR if someone else wants it in Boost.

---

## Comment 1

> Username: Becheler  
> Created at: 2026-01-10 17:57:11 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3733292598  

Hi @np96  ! Thank you for your interest !  
  
Great question!  If I understand correctly, the weighted matching implementation is actually quite recent: Yi Ji added it in 2018, and Joris van Rantwijk updated it just this year. So this seems to be evolving.  
Also performance optimizations sometimes take longer to prioritize than correctness fixes, so Blossom V I guess had weaker priority.  
  
Anyway, I downloaded the paper and will assess implementation feasibility. I will let you know asap :)

---

## Comment 2

> Username: np96  
> Created at: 2026-01-10 19:29:59 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3733421284  

@Becheler hi there! Thanks for your response.  
  
Sorry, I messed things a bit — I am not interested in ordinary matching implementation. The one which is in the Boost is enough for me.  
  
The thing I need: Weighted b-matching solver: each vertex is additionaly labeled by `b_v` and if `v` is in the solution it has (up to) `b_v` incident edges. Also might be known as degree-constrained subgraph problem.  
  
Tried to search for any implementation for around 1 week with no luck.  
Sorry again for mixing things up. GPTs are sometimes saying convincing nonsense.

---

## Comment 3

> Username: Becheler  
> Created at: 2026-01-10 19:44:35 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3733431809  

No worries at all, graphs and matching problems tend to be quite polymorphic and hard to identify.  
I am not sure what @jeremy-murphy  would say but it seems to me like a much harder problem.

---

## Comment 4

> Username: np96  
> Created at: 2026-01-11 10:13:28 UTC  
> Updated at: 2026-01-11 10:19:34 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3734353486  

By the way, a simple and beautiful reduction to ordinary matching problem by [Shiloach](https://web.eecs.umich.edu/~pettie/matching/Shiloach-another-look-at-degree-constrained-subgraph.pdf) can be applied. Probably not the most efficient approach, but looks like the most simple to implement.  
  
Would like to hear an expert opinion if this might be needed in Boost.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2026-01-15 01:08:44 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3752404849  

Yeah I'm a little surprised that an old fundamental algorithm wasn't implemented yet but I don't know enough of the theory here to know whether these variant problems are easily reducible to what we currently have. I need to spend some time familiarizing myself with the theory, etc.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2026-01-15 01:09:11 UTC  
> Url: https://github.com/boostorg/graph/issues/446#issuecomment-3752405796  

Or if someone else can figure it out and explain it here, that would be great too.  :)
