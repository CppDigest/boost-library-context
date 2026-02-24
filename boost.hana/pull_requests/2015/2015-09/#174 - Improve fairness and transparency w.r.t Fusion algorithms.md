# #174 [Benchmarks] Improve fairness and transparency w.r.t Fusion algorithms [Merged]

> Username: ldionne  
> Created at: 2015-09-07 13:18:31 UTC  
> Updated at: 2015-10-23 12:48:29 UTC  
> Merged at: 2015-10-23 11:57:27 UTC  
> Closed at: 2015-10-23 11:57:27 UTC  
> Url: https://github.com/boostorg/hana/pull/174  

Specifically,  
  
(1) We now use `fusion::for_each` to evaluate the results instead of `fusion::as_vector`  
(2) We now benchmark with fusion::list too  
(3) We now document our methodology for forcing the evaluation of algorithms  
  
This is in response mainly to [this thread](http://boost.2283326.n4.nabble.com/Hana-Views-and-References-tp4677490.html) that was raised during the formal review.  
  
@djowel, could you please let me know if using `fusion::for_each` seems like a good way to force the evaluation of an algorithm? Also, I added the following notice to the section on benchmarking:  
  
> Before we move on, it is important to mention something regarding the benchmark methodology for Fusion algorithms. Some algorithms in Fusion are lazy, which means that they don't actually perform anything, but simply return a modified view to the original data. This is the case of `fusion::transform`, which simply returns a transformed view that applies the function to each element of the original sequence as those elements are accessed. If we want to benchmark something at all, we need to force the evaluation of that view, as would eventually happen when accessing the elements of the sequence in real code anyway. To do this, we use `fusion::for_each` with a function that does not perform anything, which should not bias the benchmarks too favorably towards Hana. Benchmarking is difficult to do properly, and comparing code written with different evaluation strategies is especially tricky. For the rest of this section, we will mention when a Fusion algorithm is lazy, so that you know when we're "artificially" forcing the evaluation of the algorithm for the purpose of benchmarking.  
  
Do you think it is a fair notice? It's really important me to have meaningful benchmarks, and I'd like to have your validation before I push this.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-09-07 19:01:40 UTC  
> Updated_at: 2015-09-07 19:36:38 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138359376  

Well, not really. It will only be "fair" if you are also testing views. So Apples to Oranges... Hana's performance tests ignore the inherent advantage of Fusion's views. Hana does not have them.  
  
Let's take a simple example, tuple push_back. Imagine pushing back to a big tuple N times. With eager evaluation, you will end up with N massive tuples, each time with N+1 elements. With views, you will get a lightweight joint_view<S, single_view<T> for each push_back (where S is the original sequence and T is the type of the new element). No copies, no big tuple construction.  
  
This simple example is idiomatic. It is representative of a general pattern on how algorithms are applied to tuples. You have one or more tuples and apply one or more algorithms over the tuples. Eager evaluation will give you N temporary tuple results, one for each algorithm. With lazy evaluation there will be no such temporaries. _If_ necessary, you can assign the _final_ result to a result tuple.  
  
Delaying the computation as late as possible is a well known runtime optimisation strategy. In many cases, you won't even need to evaluate everything; only a range of results, or maybe a folded result, or perhaps a true/false result from algorithms like any, all or none, etc.  
  
Forcing full evaluation of lazy algorithms is naive at best (edited from cheating) and does not reflect their intent and real optimisation potential.

---

## Comment 2

> Username: jfalcou  
> Created_at: 2015-09-07 19:46:27 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138367634  

> Eager evaluation will give you N temporary tuple results, one for each algorithm. With lazy   
> evaluation there will be no such temporaries. If necessary, you can assign the final result    
> to a result tuple.  
  
Wouldn't a proper compiler optimize those usless temporary through copy elision ?

---

## Comment 3

> Username: djowel  
> Created_at: 2015-09-07 19:54:30 UTC  
> Updated_at: 2015-09-07 19:54:48 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138368417  

In simple cases, yes. But in my experience, the compiler gets confused once you have a healthy mix of algorithm calls, especially if the calls are distributed all over the place. Remember proto's 2-stage evaluation? That is an example. You would have thought that copying by value would be optimized away by the compiler in the first phase. No the compiler did not. And so, we had to come up with some hold-the-tree-by-reference schemes to give good runtime performance.

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-09-07 20:14:03 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138369742  

@djowel I would argue that forcing the evaluation is not the actual problem, since there's no way to benchmark anything otherwise.  
  
IMO, the problem is that we're not benchmarking complex enough examples to do a proper comparison. In the simple cases presented in the tutorial, Hana will effectively outperform Fusion, and this is hardly debatable since we have data to back it up. The issue seems to be that we're not considering complex enough examples, where Fusion's view may have an advantage over eager evaluation.  
  
So, would it be acceptable to add another benchmark where we nest multiple levels of algorithms **and then** force the evaluation of Fusion (because otherwise there's nothing to measure)? This way, we could see whether the temporaries hurt performance and so on.  
  
In particular, would you have a good benchmark to suggest for Fusion? I could try to implement it with Hana and we could measure.  
  
Also, for full disclosure; I don't think Hana beats Fusion in all cases like the tutorial suggests it, which is why I'd like to correct the situation. I _do_ think that Hana code is more easily optimized by compilers _in simple cases at least_, but I also think that Fusion would beat Hana anytime there's something very costly to move around. I'd like to work hand-in-hand with Fusion devs so we can figure out in which cases laziness is the way to go, and in which cases it isn't. I think that is beneficial for everybody.

---

## Comment 5

> Username: djowel  
> Created_at: 2015-09-07 20:15:54 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138369856  

Anyway, try it, just for fun. Every once in a while I think to myself that C++ is smart enough so we can pass and return by value without care and the compiler will optimize away. This is what Dave (A) advocates, for many years. So, every once in a while, I try it out. But alas, every time I do, I still get overhead and so revert to const& or even &.

---

## Comment 6

> Username: djowel  
> Created_at: 2015-09-07 20:24:48 UTC  
> Updated_at: 2015-09-07 20:28:43 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138370467  

Re Views: It's a must in the runtime world. Recall Eric (N) Range presentation. Imagine the range library without views. In particular, imagine the full page code of concatenated algorithms that gave the nice formatted calendar. If copy and return by value is indeed the silver bullet, thanks to compiler optimizations, then the Range library does not need them (views). But it does.

---

## Comment 7

> Username: djowel  
> Created_at: 2015-09-08 00:29:52 UTC  
> Updated_at: 2015-09-08 00:30:14 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-138396715  

Tell you what... I'm very busy right now and I can't find the time to do some fusion work. At the very least, there shouldn't be a reason why Hana will be faster on runtime on similar and simple (non-lazy) operations. If there are such cases, I'd say they are bugs that should be fixed. That said, Kohei Takahashi (https://github.com/Flast) is working on fusion optimizations and modernization right now. Perhaps you can work hand-in-hand with him, as you say. If you do, please keep me in the loop.

---

## Comment 8

> Username: ldionne  
> Created_at: 2015-10-23 11:58:18 UTC  
> Url: https://github.com/boostorg/hana/pull/174#issuecomment-150552969  

Note that we still use `as_list` and `as_vector` to force the evaluation  
of algorithms instead of using e.g. `for_each`. This is because we want  
to compare apples with apples, and for this we need to get a sequence of  
computed values, not only for_each over the view. The disclaimer in the  
tutorial saying "Fusion might encourage a different design" takes care  
of warning people about the fact that we're not necessarily using  
idiomatic Fusion, but not need to benchmark unfairly to try to  
account for that.  
  
However, we'll consider adding views and benchmarking views vs views, too.

---
