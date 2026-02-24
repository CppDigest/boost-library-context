# #398 - `is_connected()` is too slow [Open]

> Username: Wavetrace  
> Created at: 2024-11-12 13:56:02 UTC  
> Updated at: 2024-12-11 23:09:05 UTC  
> Url: https://github.com/boostorg/graph/issues/398  

is_connected function is documented as   
  
```  
// Is the undirected graph connected?  
// Is the directed graph strongly connected?  
```  
  
and achieves this goal by checking if each vertex is reachable from each other one. This is unnecessarily resource-consuming and slow, O(n^3) (or even O(n^4)). You could call this a performance bug.  
  
Please see PR397 (https://github.com/boostorg/graph/pull/397) that fixes this to run in O(N) (O(N+E)).  
  
See the attached log_vec.txt for speed evaluation.  
[log-vec.txt](https://github.com/user-attachments/files/17717572/log-vec.txt)  
(please tell if you're interested in the program that generates it)

---

## Comment 1

> Username: Wavetrace  
> Created at: 2024-12-11 23:09:04 UTC  
> Url: https://github.com/boostorg/graph/issues/398#issuecomment-2537387173  

As discussed in PR, it might be more reasonable to make the interface more explicit / less confusing rather than to preserve 100% backward compatibility. PR has been updated with this in mind.
