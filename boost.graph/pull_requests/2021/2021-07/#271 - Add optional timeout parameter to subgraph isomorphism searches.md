# #271 Add optional timeout parameter to subgraph isomorphism searches. [Closed]

> Username: cqc-alec  
> Created at: 2021-07-06 15:04:22 UTC  
> Updated at: 2021-12-16 21:56:46 UTC  
> Closed at: 2021-12-14 10:27:36 UTC  
> Url: https://github.com/boostorg/graph/pull/271  

Currently the only way to time out a search is from the user-provided callback, but the callback is only called when a match is found. This PR provides a way to set a timeout on the search irrespective of whether or when a match is found.

---

## Comment 1

> Username: cqc-alec  
> Created_at: 2021-07-06 15:47:21 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-874874922  

Apologies, I have obviously failed to set up linkage with `boost::chrono`. How to do that?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2021-12-05 06:58:09 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-986177681  

I'm personally not really keen on this idea because it's an optional feature that is orthogonal to the purpose of the algorithm itself, I mean, users could request a timeout parameter on any algorithm. What's wrong the user just using a timer feature from their operating system or from the standard library's threads?

---

## Comment 3

> Username: cqc-alec  
> Created_at: 2021-12-05 09:24:02 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-986194810  

> I'm personally not really keen on this idea because it's an optional feature that is orthogonal to the purpose of the algorithm itself, I mean, users could request a timeout parameter on any algorithm. What's wrong the user just using a timer feature from their operating system or from the standard library's threads?  
  
I did consider running the algorithm in a thread but the problem is that there is no way (without modifying the boost code in some way) to force it to terminate after a certain time. (The callback is no help because it may be called infrequently or not at all.)   
  
Maybe Boost.Process could help, though I suspect this would necessitate a lot of extra complexity.  
  
I do agree with your point though. Probably we should not really be using this algorithm in cases where very long searches are likely to be an issue. (Our use case involves attempting to solve a problem, then if no solution is found in a certain time making the problem slightly easier, and so on until a solution is found.)

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2021-12-09 02:31:01 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-989447979  

Hmmm, right. Yeah, I can certainly see the use case for a timeout.  
  
As you mentioned, the callback is not called frequently enough, so maybe the answer is: another callback! No, seriously. The search algorithms, for example, have a visitor with callbacks for every step of the algorithm. We could have something similar here so that users can terminate their search after searching _n_ vertices, or in your case, _n_ seconds, etc.

---

## Comment 5

> Username: cqc-alec  
> Created_at: 2021-12-09 10:32:37 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-989723748  

> Hmmm, right. Yeah, I can certainly see the use case for a timeout.  
>   
> As you mentioned, the callback is not called frequently enough, so maybe the answer is: another callback! No, seriously. The search algorithms, for example, have a visitor with callbacks for every step of the algorithm. We could have something similar here so that users can terminate their search after searching _n_ vertices, or in your case, _n_ seconds, etc.  
  
I like the idea of another callback! That also means we don't have to depend on `boost::chrono`. I will work on implementing this.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2021-12-09 21:54:42 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-990314541  

Thanks, much appreciated. Please feel free to discuss your design ideas here before doing a full implementation. Adding a new callback could be a delicate operation and we want to make sure it fits with the existing design philosophy.

---

## Comment 7

> Username: cqc-alec  
> Created_at: 2021-12-14 10:27:36 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-993396651  

Closing this PR. Will open another one for the callback idea.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2021-12-16 21:56:46 UTC  
> Url: https://github.com/boostorg/graph/pull/271#issuecomment-996224301  

> Closing this PR. Will open another one for the callback idea.  
  
OK, sounds good.

---
