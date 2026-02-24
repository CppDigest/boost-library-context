# #18 - uneven_block distribution does not handle empty processes correctly [Open]

> Username: alexrobomind  
> Created at: 2019-09-27 12:11:13 UTC  
> Updated at: 2019-10-11 19:01:41 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/18  

When creating an instance of uneven_block with the distribution [1, 0, 1] then the implementation incorrectly returns process id 1 (counting from 0) to be the owner of element id 1 (counting from zero), because the local starts of processes 1 and 2 are identical (local starts would be [0, 1, 1, 2] in this case).  
  
In general, the implementation incorrectly assigns elements belonging to the first owner after one or more adjacent empty owners to the first of those empty ones.  
  
I would suggest to change the implementation from  
https://github.com/boostorg/graph_parallel/blob/22385ccb6c35fcb07971f295807519bd031ed57d/include/boost/graph/parallel/distribution.hpp#L225-L226  
to  
```  
size_vector::const_iterator ub = std::upper_bound(local_starts.begin(), local_starts.end(), (std::size_t) i);  
return (--ub) - local_starts.begin();  
```  
  
Edit: Corrected mistake as pointed out below.

---

## Comment 1

> Username: fearsomepirate  
> Created at: 2019-10-11 15:23:41 UTC  
> Updated at: 2019-10-11 16:49:35 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/18#issuecomment-541109563  

I just ran into this bug, can confirm. I have a graph on 3 processes with 4/0/4 distribution, and it crashes due to incorrectly converting index value of 4 to {local, owner} = {0,1}.  
  
By the way, that fix should say (--ub).

---

## Comment 2

> Username: alexrobomind  
> Created at: 2019-10-11 19:01:41 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/18#issuecomment-541185838  

Oops. Edited.
