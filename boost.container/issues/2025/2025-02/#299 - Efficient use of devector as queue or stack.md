# #299 - Efficient use of devector as queue or stack. [Open]

> Username: karl3wm  
> Created at: 2025-02-18 16:06:07 UTC  
> Updated at: 2025-04-19 21:17:29 UTC  
> Url: https://github.com/boostorg/container/issues/299  

The current policy of devector is to move the data to the middle of the allocated region whenever capacity is needed on an end.  
  
This results in half of the free capacity being wasted when only one end of the devector is used.  
  
Instead, the policy for free capacity for the devector should be configurable separately for each end.  
  
This would make it easy to provide configurations for contiguous queues.  
  
Additionally, the functions to reserve capacity in the front or back always allocate if space is needed. Instead, if sufficient capacity per the policy is available on the other end, the data could be moved without the new allocation. This is most appropriate for queues which have need of capacity on only one end.  
  
Avoiding problems like this would ideally be done by parameterizing the algorithmic choices of devector into general policies.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-19 21:17:27 UTC  
> Url: https://github.com/boostorg/container/issues/299#issuecomment-2816869592  

If only one end of the devector is used, then you shouldn't use devector, but vector. devector is optimized for the use cases when both ends are used.
