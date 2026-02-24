# #332 - array growth policy [Closed]

> Username: vinniefalco  
> Created at: 2020-09-14 00:58:46 UTC  
> Updated at: 2020-09-20 20:45:25 UTC  
> Closed at: 2020-09-20 20:45:25 UTC  
> Url: https://github.com/boostorg/json/issues/332  

We removed the minimum array capacity to resolve #314 but now we have a new problem. Inserting elements one at a time into an array incurs more allocations than necessary. Capacity goes from 0 to 1, then from 1 to 2, then from 2 to 4, etc...  
  
We probably want to have a minimum size of 3, and use a 1.5x growth factor instead of 2x.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-20 20:45:25 UTC  
> Url: https://github.com/boostorg/json/issues/332#issuecomment-695834191  

Array uses 1.5x now and has been refactored / rewritten
