# #162 Changed the output [Open]

> Username: sreejithsankar55  
> Created at: 2019-02-12 13:06:36 UTC  
> Updated at: 2024-07-16 00:23:40 UTC  
> Url: https://github.com/boostorg/graph/pull/162  

Fix: #161   
  
Tested and fixed the output in the file subgraph.cpp and subgraph.expected

---

## Comment 1

> Username: anadon  
> Created_at: 2019-04-05 20:42:11 UTC  
> Url: https://github.com/boostorg/graph/pull/162#issuecomment-480415480  

Could you be more literal in what is incorrect?  It looks like you're changing documentation and no code.

---

## Comment 2

> Username: sreejithsankar55  
> Created_at: 2019-04-06 04:01:47 UTC  
> Url: https://github.com/boostorg/graph/pull/162#issuecomment-480471996  

> Could you be more literal in what is incorrect? It looks like you're changing documentation and no code.  
@anadon The output in the documentation of subgraph.expected and the commented part of subgraph.cpp is wrong when I tested the code locally, it produces a different output rather than the expected. Hence, I changed it to exactly what the code produces as the output.

---

## Comment 3

> Username: anadon  
> Created_at: 2019-04-06 14:29:36 UTC  
> Url: https://github.com/boostorg/graph/pull/162#issuecomment-480508576  

I have serious misgivings about correctness here.  Those examples are old and when they were originally made they were *very* likely validated.  It it possible internal behavior has changes or there is a bug, but you're going to need to provide a test case.  
  
How are you generating your results?  Please include your code.

---
