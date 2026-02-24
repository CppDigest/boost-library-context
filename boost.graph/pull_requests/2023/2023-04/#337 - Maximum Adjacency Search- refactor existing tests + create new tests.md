# #337 Maximum Adjacency Search: refactor existing tests + create new tests [Merged]

> Username: daankolthof  
> Created at: 2023-04-27 09:22:49 UTC  
> Updated at: 2023-05-01 23:24:50 UTC  
> Merged at: 2023-04-30 23:22:10 UTC  
> Closed at: 2023-04-30 23:22:10 UTC  
> Url: https://github.com/boostorg/graph/pull/337  

See https://github.com/boostorg/graph/issues/297.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-04-28 01:22:02 UTC  
> Url: https://github.com/boostorg/graph/pull/337#issuecomment-1526848625  

Awesome, looks great!  
  
I don't have time to comb through it in detail, but one possible subtle issue comes to mind to do with tie-breaking and order of construction of the graph, etc.  
  
What I mean is, is it possible that MAS might choose different vertices when breaking ties, depending on what order the vertices were added to the graph? But your tests are testing for one specific sequence of vertices? We don't want the tests to be overly strict about tie-breaking logic.  
  
Have a think about it, maybe play around with creating the vertices in different orders or just convince yourself that the result will be the same regardless.

---

## Comment 2

> Username: daankolthof  
> Created_at: 2023-04-30 21:43:20 UTC  
> Url: https://github.com/boostorg/graph/pull/337#issuecomment-1529145613  

> Awesome, looks great!  
>   
> I don't have time to comb through it in detail, but one possible subtle issue comes to mind to do with tie-breaking and order of construction of the graph, etc.  
>   
> What I mean is, is it possible that MAS might choose different vertices when breaking ties, depending on what order the vertices were added to the graph? But your tests are testing for one specific sequence of vertices? We don't want the tests to be overly strict about tie-breaking logic.  
>   
> Have a think about it, maybe play around with creating the vertices in different orders or just convince yourself that the result will be the same regardless.  
  
The current implementation of the MAS search is deterministic, so there is no random/unpredictable tie-breaking. With regards to the testing, the unit tests have always been focused on testing this specific implementation, even before my refactoring. This means that we always expect one specific visiting order for each graph during our tests.  
  
As you correctly point out, the MAS search itself can have multiple correct visiting orders. We can, if we want to, refactor the tests to account for these multiple visiting orders. In this case, we don’t define one correct order with one correct set of weights.  
  
If we decide to refactor these tests, we will be less precise in our testing as we have no easy way to test for correct visiting orders. However, we can still test the ‘weights when visited’ vector, as we can still calculate this weight based on earlier visited nodes and their edges.  
  
Let me know if this is something you want to consider, personally I think it would decrease the quality of our testing too much. Given that the current implementation always expects an updateable maxheap for priority tracking, there is no way that the current implementation would ever fail the existing or new tests.  
  
If the vertices are in different order, or we run the algorithm with a different starting vertex (which I have already incorporated in the tests), we are indeed expecting different outcomes (which are incorporated in the newly added tests).

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-04-30 23:21:44 UTC  
> Url: https://github.com/boostorg/graph/pull/337#issuecomment-1529164661  

I would still like to consider relaxing the tests to account for multiple correct orderings, but I think it can wait until another time. What you've done here is great and we should merge it now so that we can turn out attention to the implementation next. The need for more relaxed tests might come up later. I'll make a ticket for it and tag you on it, but it's not urgent.

---
